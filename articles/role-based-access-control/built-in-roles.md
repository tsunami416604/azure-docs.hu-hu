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
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 17a1f2c245e19afbf4d8c5092a0ddf0562a7cb0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979327"
---
# <a name="built-in-roles-for-azure-resources"></a>Beépített szerepkörök az Azure-erőforrásokhoz

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) számos beépített szerepkörrel rendelkezik az Azure-erőforrásokhoz, amelyeket hozzárendelhet a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz. A szerepkör-hozzárendelések lehetővé teszik az Azure-erőforrásokhoz való hozzáférés szabályozását. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni szerepköröket az Azure-erőforrásokhoz](custom-roles.md).

Ez a cikk az Azure-erőforrások beépített szerepköreit sorolja fel, amelyek folyamatosan fejlődnek. A legújabb szerepkörök beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Ha a Azure Active Directory rendszergazdai szerepköreit keresi, tekintse meg a [rendszergazdai szerepkör engedélyeit Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Beépített szerepkör-leírások

A következő táblázat az egyes beépített szerepkörök rövid leírását tartalmazza. A szerepkör nevére kattintva megtekintheti az egyes szerepkörökhöz tartozó `Actions`, `NotActions`, `DataActions`és `NotDataActions` listáját. További információ ezekről a műveletekről, valamint a kezelési és adatsíkokra való alkalmazásáról: az [Azure-erőforrások szerepkör-definícióinak megismerése](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Beépített szerepkör | Leírás | Azonosító |
> | --- | --- | --- |
> | [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Közreműködő](#contributor) | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférés biztosítását. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [AcrDelete](#acrdelete) | ACR törlése | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-rendszerkép aláírója | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR-lekérés | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR leküldése | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR karanténba helyezési Adatolvasó | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | az ACR karanténba helyezési adatírója | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [API Management szolgáltatás közreműködői](#api-management-service-contributor) | Képes a szolgáltatás és az API-k kezelésére | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management Service operátori szerepkör](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de nem az API-kat | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management szolgáltatás-olvasó szerepkör](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Application Insights összetevő közreműködője](#application-insights-component-contributor) | Felügyelheti Application Insights összetevőket | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Automation-feladatok operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-Runbookok használatával. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operátor](#automation-operator) | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook operátor](#automation-runbook-operator) | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Avere közreműködője](#avere-contributor) | Létrehozhat és kezelhet egy avere vFXT-fürtöt. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere operátor](#avere-operator) | A avere vFXT-fürt által használt fürt kezelése | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Az Azure Event Hubs adattulajdonos](#azure-event-hubs-data-owner) | Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs adatfogadó](#azure-event-hubs-data-receiver) | Engedélyezi az Azure Event Hubs-erőforrások elérését. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs adatfeladó](#azure-event-hubs-data-sender) | Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Azure Kubernetes Service-fürt rendszergazdai szerepköre](#azure-kubernetes-service-cluster-admin-role) | A fürt rendszergazdai hitelesítő adatainak listázása művelet. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes Service-fürt felhasználói szerepköre](#azure-kubernetes-service-cluster-user-role) | Fürt felhasználói hitelesítő adatainak listázása. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Maps Adatolvasó (előzetes verzió)](#azure-maps-data-reader-preview) | Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Sentinel közreműködő](#azure-sentinel-contributor) | Azure Sentinel közreműködő | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-olvasó](#azure-sentinel-reader) | Azure Sentinel-olvasó | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel-válaszadó](#azure-sentinel-responder) | Azure Sentinel-válaszadó | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Azure Service Bus adattulajdonos](#azure-service-bus-data-owner) | Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Adatfogadó Azure Service Bus](#azure-service-bus-data-receiver) | Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Adatfeladó Azure Service Bus](#azure-service-bus-data-sender) | Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack regisztrációs tulajdonos](#azure-stack-registration-owner) | Lehetővé teszi Azure Stack regisztrációk kezelését. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Biztonsági mentési közreműködő](#backup-contributor) | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Biztonságimásolat-felelős](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Backup-olvasó](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Számlázási olvasó](#billing-reader) | Olvasási hozzáférés engedélyezése a számlázási információkhoz | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [BizTalk közreműködő](#biztalk-contributor) | Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Blockchain-hozzáférés (előzetes verzió)](#blockchain-member-node-access-preview) | Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Tervezet közreműködője](#blueprint-contributor) | Kezelheti a terv definícióit, de nem rendelheti hozzá őket. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Blueprint operátor](#blueprint-operator) | Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új tervrajzokat. Megjegyzés: ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [CDN-végpont közreműködői](#cdn-endpoint-contributor) | Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-végpont olvasója](#cdn-endpoint-reader) | Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil közreműködői](#cdn-profile-contributor) | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profil olvasója](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Klasszikus Storage-fiók közreműködői](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#classic-storage-account-key-operator-service-role) | A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Klasszikus virtuális gép közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Cognitive Services közreműködő](#cognitive-services-contributor) | Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services Adatolvasó (előzetes verzió)](#cognitive-services-data-reader-preview) | Lehetővé teszi Cognitive Services-információk olvasását. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services felhasználó](#cognitive-services-user) | Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | [Cosmos DB fiók-olvasó szerepkör](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operátor](#cosmos-db-operator) | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Cost Management közreműködő](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management olvasó](#cost-management-reader) | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) | 72fafb9e-0641-4937-9268 – a91bfd8191a3 |
> | [Data Box közreműködő](#data-box-contributor) | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box olvasó](#data-box-reader) | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Factory közreműködő](#data-factory-contributor) | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Lake Analytics fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. | 47b7735b-770E-4598-a7da-8b91488b4c88 |
> | [Adattisztító](#data-purger) | Törölheti az elemzési adatfájlokat | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [DevTest Labs-felhasználó](#devtest-labs-user) | Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [DNS-zóna közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [DocumentDB-fiók közreműködői](#documentdb-account-contributor) | Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [EventGrid EventSubscription közreműködője](#eventgrid-eventsubscription-contributor) | Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-olvasó](#eventgrid-eventsubscription-reader) | Lehetővé teszi a EventGrid esemény-előfizetések olvasását. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [HDInsight-fürt operátora](#hdinsight-cluster-operator) | Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight tartományi szolgáltatások közreműködője](#hdinsight-domain-services-contributor) | Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Intelligens rendszer-fiók közreműködői](#intelligent-systems-account-contributor) | Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Key Vault közreműködő](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de azokhoz való hozzáférés nélkül. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Tesztkörnyezet létrehozója](#lab-creator) | Lehetővé teszi a felügyelt Labs Azure Lab-fiókokban való létrehozását, kezelését és törlését. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Log Analytics közreműködő](#log-analytics-contributor) | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics olvasó](#log-analytics-reader) | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Logikai alkalmazás közreműködői](#logic-app-contributor) | Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic app-operátor](#logic-app-operator) | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Felügyelt alkalmazás operátori szerepköre](#managed-application-operator-role) | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Felügyelt identitás közreműködői](#managed-identity-contributor) | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Managed Identity Operator](#managed-identity-operator) | Felhasználóhoz rendelt identitás olvasása és hozzárendelése | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Managed Services Registration assignment Delete Role](#managed-services-registration-assignment-delete-role) | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Management Group Contributor](#management-group-contributor) | Felügyeleti csoport közreműködői szerepköre | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Management Group Reader](#management-group-reader) | Felügyeleti csoport olvasójának szerepköre | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Monitoring Contributor](#monitoring-contributor) | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Monitoring Metrics Publisher](#monitoring-metrics-publisher) | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring Reader](#monitoring-reader) | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [New Relic APM Account Contributor](#new-relic-apm-account-contributor) | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (Preview)](#policy-insights-data-writer-preview) | Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Reader and Data Access](#reader-and-data-access) | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Redis Cache Contributor](#redis-cache-contributor) | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Resource Policy Contributor](#resource-policy-contributor) | Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Scheduler Job Collections Contributor](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Search Service Contributor](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Security Admin](#security-admin) | Csak Security Center esetén: megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, biztonsági házirendeket szerkeszthet, megtekintheti a riasztásokat és a javaslatokat | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Security Manager (Legacy)](#security-manager-legacy) | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Security Reader](#security-reader) | Csak Security Center esetén: megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Site Recovery Contributor](#site-recovery-contributor) | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery Operator](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery Reader](#site-recovery-reader) | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Spatial Anchors Account Contributor](#spatial-anchors-account-contributor) | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Spatial Anchors Account Owner](#spatial-anchors-account-owner) | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Spatial Anchors Account Reader](#spatial-anchors-account-reader) | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [SQL DB Contributor](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL Managed Instance Contributor](#sql-managed-instance-contributor) | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server Contributor](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Tárfiók-közreműködő](#storage-account-contributor) | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#storage-account-key-operator-service-role) | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage blob adatközreműködői](#storage-blob-data-contributor) | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage blob-adattulajdonos](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage blob-Adatolvasó](#storage-blob-data-reader) | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage blob-delegáló](#storage-blob-delegator) | Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)című témakörben talál. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage file-adatsmb-megosztás közreműködői](#storage-file-data-smb-share-contributor) | Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage file-adatsmb-megosztás emelt szintű közreműködője](#storage-file-data-smb-share-elevated-contributor) | Olvasási, írási, törlési és módosítási engedélyek engedélyezése az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli eléréséhez | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fájl adatsmb-megosztásának olvasója](#storage-file-data-smb-share-reader) | Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás SMB használatával | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Tárolási várólista adatközreműködői](#storage-queue-data-contributor) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Tárolási üzenetsor adatüzenet-processzora](#storage-queue-data-message-processor) | Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Tárolási várólista adatüzenetének küldője](#storage-queue-data-message-sender) | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Storage-várólista adatolvasója](#storage-queue-data-reader) | Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Támogatási kérelem közreműködői](#support-request-contributor) | Lehetővé teszi a támogatási kérések létrehozását és kezelését | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Traffic Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Felhasználói hozzáférés rendszergazdája](#user-access-administrator) | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Virtuális gép rendszergazdai bejelentkezése](#virtual-machine-administrator-login) | Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtuális gép felhasználói bejelentkezés](#virtual-machine-user-login) | Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Webes csomag közreműködői](#web-plan-contributor) | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webhely közreműködői](#website-contributor) | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk. | de139f84-1756-47ae-9be6-808fbbe84772 |


## <a name="owner"></a>Tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. |
> | **Azonosító** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="contributor"></a>Közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférés biztosítását. |
> | **Azonosító** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. Authorization/*/delete | Szerepkörök, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definícióinak törlése |
> | Microsoft. Authorization/*/Write | Szerepkörök, szerepkör-hozzárendelések, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definíciók létrehozása |
> | Microsoft. Authorization/elevateAccess/művelet | A hívónak felhasználói hozzáférésű rendszergazdai hozzáférést engedélyez a bérlői hatókörben |
> | Microsoft. Blueprint/blueprintAssignments/Write | Tervrajz-hozzárendelések létrehozása vagy frissítése |
> | Microsoft. Blueprint/blueprintAssignments/delete | Bármely tervrajz-hozzárendelés törlése |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="reader"></a>Olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat. |
> | **Azonosító** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR törlése |
> | **Azonosító** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartások/összetevők/törlés | Összetevő törlése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-rendszerkép aláírója |
> | **Azonosító** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartások/aláírás/írás | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR-lekérés |
> | **Azonosító** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/lekérés/olvasás | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR leküldése |
> | **Azonosító** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/lekérés/olvasás | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Microsoft. ContainerRegistry/nyilvántartó/leküldéses/írási | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | ACR karanténba helyezési Adatolvasó |
> | **Azonosító** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/karanténba helyezés/olvasás | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | az ACR karanténba helyezési adatírója |
> | **Azonosító** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/karanténba helyezés/olvasás | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Microsoft. ContainerRegistry/nyilvántartások/karanténba helyezés/írás | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Képes a szolgáltatás és az API-k kezelésére |
> | **Azonosító** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Műveletek** |  |
> | Microsoft. ApiManagement/szolgáltatás/* | API Management szolgáltatás létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="api-management-service-operator-role"></a>API Management szolgáltatás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a szolgáltatást, de nem az API-kat |
> | **Azonosító** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Műveletek** |  |
> | Microsoft. ApiManagement/Service/*/READ | API Management szolgáltatási példányok beolvasása |
> | Microsoft. ApiManagement/szolgáltatás/biztonsági mentés/művelet | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Microsoft. ApiManagement/szolgáltatás/törlés | API Management szolgáltatás példányának törlése |
> | Microsoft. ApiManagement/Service/managedeployments/művelet | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Microsoft. ApiManagement/szolgáltatás/olvasás | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft. ApiManagement/szolgáltatás/visszaállítás/művelet | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Microsoft. ApiManagement/Service/updatecertificate/művelet | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Microsoft. ApiManagement/Service/updatehostname/művelet | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Microsoft. ApiManagement/szolgáltatás/írás | API Management szolgáltatás új példányának létrehozása |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. ApiManagement/szolgáltatás/felhasználók/kulcsok/olvasás | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="api-management-service-reader-role"></a>API Management szolgáltatás-olvasó szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz |
> | **Azonosító** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Műveletek** |  |
> | Microsoft. ApiManagement/Service/*/READ | API Management szolgáltatási példányok beolvasása |
> | Microsoft. ApiManagement/szolgáltatás/olvasás | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. ApiManagement/szolgáltatás/felhasználók/kulcsok/olvasás | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="application-insights-component-contributor"></a>Application Insights összetevő közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelheti Application Insights összetevőket |
> | **Azonosító** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/összetevők/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft. bepillantások/webteszt/* | Webes tesztek létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. |
> | **Azonosító** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/összetevők/*/READ |  |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="automation-job-operator"></a>Automation-feladatok operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Feladatok létrehozása és kezelése Automation-Runbookok használatával. |
> | **Azonosító** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/READ | Hibrid Runbook Worker-erőforrások beolvasása |
> | Microsoft. Automation/automationAccounts/feladatok/olvasás | Azure Automation feladatok beolvasása |
> | Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet | Egy Azure Automation feladatot folytat |
> | Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet | Leállítja egy Azure Automation feladatot |
> | Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás | Beolvas egy Azure Automation-feladatok streamjét |
> | Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet | Azure Automation feladatok felfüggesztése |
> | Microsoft. Automation/automationAccounts/feladatok/írás | Létrehoz egy Azure Automation feladatot |
> | Microsoft. Automation/automationAccounts/feladatok/kimenet/olvasás | A feladatok kimenetének beolvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="automation-operator"></a>Automation-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására |
> | **Azonosító** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/READ | Hibrid Runbook Worker-erőforrások beolvasása |
> | Microsoft. Automation/automationAccounts/feladatok/olvasás | Azure Automation feladatok beolvasása |
> | Microsoft. Automation/automationAccounts/feladatok/folytatás/művelet | Egy Azure Automation feladatot folytat |
> | Microsoft. Automation/automationAccounts/feladatok/leállítás/művelet | Leállítja egy Azure Automation feladatot |
> | Microsoft. Automation/automationAccounts/feladatok/streamek/olvasás | Beolvas egy Azure Automation-feladatok streamjét |
> | Microsoft. Automation/automationAccounts/Jobs/felfüggesztés/művelet | Azure Automation feladatok felfüggesztése |
> | Microsoft. Automation/automationAccounts/feladatok/írás | Létrehoz egy Azure Automation feladatot |
> | Microsoft. Automation/automationAccounts/jobSchedules/READ | Azure Automation feladatütemezés beolvasása |
> | Microsoft. Automation/automationAccounts/jobSchedules/Write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | Microsoft. Automation/automationAccounts/linkedWorkspace/READ | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | Microsoft. Automation/automationAccounts/READ | Azure Automation fiók beolvasása |
> | Microsoft. Automation/automationAccounts/runbookok/READ | Azure Automation runbook beolvasása |
> | Microsoft. Automation/automationAccounts/ütemezett/beolvasás | Azure Automation ütemezett eszköz beolvasása |
> | Microsoft. Automation/automationAccounts/ütemterv/írás | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Automation/automationAccounts/feladatok/kimenet/olvasás | A feladatok kimenetének beolvasása |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="automation-runbook-operator"></a>Automation Runbook operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. |
> | **Azonosító** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Automation/automationAccounts/runbookok/READ | Azure Automation runbook beolvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="avere-contributor"></a>Avere közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Létrehozhat és kezelhet egy avere vFXT-fürtöt. |
> | **Azonosító** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. számítás/*/READ |  |
> | Microsoft. számítási/availabilitySets/* |  |
> | Microsoft. számítási/virtualMachines/* |  |
> | Microsoft. számítás/lemezek/* |  |
> | Microsoft. Network/*/READ |  |
> | Microsoft. Network/networkInterfaces/* |  |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Network/virtualNetworks/alhálózatok/olvasás | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft. Network/networkSecurityGroups/csatlakozás/művelet | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/*/READ |  |
> | Microsoft. Storage/storageAccounts/* |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/olvasás | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/írás | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="avere-operator"></a>Avere operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A avere vFXT-fürt által használt fürt kezelése |
> | **Azonosító** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Műveletek** |  |
> | Microsoft. számítás/virtualMachines/olvasás | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft. Network/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft. Network/networkInterfaces/Write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Network/virtualNetworks/alhálózatok/olvasás | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft. Network/networkSecurityGroups/csatlakozás/művelet | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/olvasás | Tárolók listájának visszaadása |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/írás | A blob-tároló Put eredményét adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/olvasás | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/írás | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-event-hubs-data-owner"></a>Az Azure Event Hubs adattulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését. |
> | **Azonosító** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Műveletek** |  |
> | Microsoft. EventHub/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs adatfogadó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi az Azure Event Hubs-erőforrások elérését. |
> | **Azonosító** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Műveletek** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs adatfeladó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését. |
> | **Azonosító** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Műveletek** |  |
> | Microsoft. EventHub/*/eventhubs/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service-fürt rendszergazdai szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A fürt rendszergazdai hitelesítő adatainak listázása művelet. |
> | **Azonosító** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Műveletek** |  |
> | Microsoft. Tárolószolgáltatás/managedClusters/listClusterAdminCredential/művelet | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service-fürt felhasználói szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Fürt felhasználói hitelesítő adatainak listázása. |
> | **Azonosító** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Műveletek** |  |
> | Microsoft. Tárolószolgáltatás/managedClusters/listClusterUserCredential/művelet | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps Adatolvasó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz. |
> | **Azonosító** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-sentinel-contributor"></a>Azure Sentinel közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Sentinel közreműködő |
> | **Azonosító** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motor használatával. |
> | Microsoft. OperationalInsights/munkaterületek/olvasás | Meglévő munkaterület beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/* |  |
> | Microsoft. OperationsManagement/megoldások/olvasás | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/* |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-sentinel-reader"></a>Azure Sentinel-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Sentinel-olvasó |
> | **Azonosító** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motor használatával. |
> | Microsoft. OperationalInsights/munkaterületek/olvasás | Meglévő munkaterület beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Microsoft. OperationsManagement/megoldások/olvasás | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-sentinel-responder"></a>Azure Sentinel-válaszadó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Sentinel-válaszadó |
> | **Azonosító** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/*/READ |  |
> | Microsoft. SecurityInsights/esetek/* |  |
> | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motor használatával. |
> | Microsoft. OperationalInsights/munkaterületek/olvasás | Meglévő munkaterület beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Microsoft. OperationsManagement/megoldások/olvasás | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-service-bus-data-owner"></a>Azure Service Bus adattulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz. |
> | **Azonosító** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Műveletek** |  |
> | Microsoft. ServiceBus/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-service-bus-data-receiver"></a>Adatfogadó Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz. |
> | **Azonosító** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Műveletek** |  |
> | Microsoft. ServiceBus/*/Queues/READ |  |
> | Microsoft. ServiceBus/*/topics/READ |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-service-bus-data-sender"></a>Adatfeladó Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését. |
> | **Azonosító** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Műveletek** |  |
> | Microsoft. ServiceBus/*/Queues/READ |  |
> | Microsoft. ServiceBus/*/topics/READ |  |
> | Microsoft. ServiceBus/*/topics/Subscriptions/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="azure-stack-registration-owner"></a>Azure Stack regisztrációs tulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Azure Stack regisztrációk kezelését. |
> | **Azonosító** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Műveletek** |  |
> | Microsoft. AzureStack/regisztráció/termékek/*/Action |  |
> | Microsoft. AzureStack/regisztráció/termékek/olvasás | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Microsoft. AzureStack/regisztráció/olvasás | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="backup-contributor"></a>Biztonsági mentési közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak |
> | **Azonosító** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationResults/* | A művelet eredményeinek kezelése a biztonsági mentési felügyeletben |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/* | Biztonsági mentési tárolók létrehozása és kezelése Recovery Services-tár biztonsági mentési hálójában |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/refreshContainers/művelet | Frissíti a tárolók listáját |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobsExport/művelet | Feladatok exportálása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupManagementMetaData/* | A biztonsági mentési felügyelettel kapcsolatos Meta-adat létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/* | Biztonsági mentési szabályzatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectedItems/* | Biztonsági másolatba mentett elemek létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionContainers/* | Tartalék elemeket tároló tárolók létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupSecurityPIN/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/tanúsítványok/* | Biztonsági mentéshez kapcsolódó tanúsítványok létrehozása és kezelése Recovery Services-tárolóban |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/* | Recovery Services-tároló használatának létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupstorageconfig/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupconfig/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupValidateOperation/művelet | A művelet érvényesítése védett elemen |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/írás | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectableContainers/READ | Az összes védhető tároló beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/monitoringAlerts/írás | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="backup-operator"></a>Biztonsági mentési operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést. |
> | **Azonosító** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationResults/READ | A művelet állapotának visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/operationResults/READ | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/biztonsági mentés/művelet | A védett elemek biztonsági mentését végzi. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationResults/READ | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationsStatus/READ | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/művelet | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/READ | A védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/Restore/Action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/művelet | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/Write | Védett elemek biztonsági másolatának létrehozása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/READ | Az összes regisztrált tároló visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/refreshContainers/művelet | Frissíti a tárolók listáját |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobsExport/művelet | Feladatok exportálása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupManagementMetaData/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/operationResults/READ | A házirend-művelet eredményeinek beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionContainers/READ | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/tanúsítványok/írás | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/extendedInformation/írás | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/registeredIdentities/írás | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupstorageconfig/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupValidateOperation/művelet | A művelet érvényesítése védett elemen |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/Write | Létrehoz egy regisztrált tárolót |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/lekérdezés/művelet | A tárolóban lévő számítási feladatok lekérdezése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/Write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/READ | Biztonsági másolat védelmi szándékának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectableContainers/READ | Az összes védhető tároló beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/Items/READ | Tároló összes elemének beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupPreValidateProtection/Action |  |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupValidateFeatures/Action | Funkciók ellenőrzése |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/monitoringAlerts/írás | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="backup-reader"></a>Backup-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat |
> | **Azonosító** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/operationResults/READ | A művelet állapotának visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/operationResults/READ | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationResults/READ | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/operationsStatus/READ | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/recoveryPoints/READ | A védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/READ | Az összes regisztrált tároló visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/operationResults/READ | A feladatok műveletének eredményét adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobs/READ | Az összes feladatütemezés visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupJobsExport/művelet | Feladatok exportálása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupManagementMetaData/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperationResults/READ | Recovery Services-tár biztonsági mentési műveletének eredményét adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/operationResults/READ | A házirend-művelet eredményeinek beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectedItems/READ | Az összes védett elem listáját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionContainers/READ | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupUsageSummaries/READ | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupstorageconfig/READ | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupconfig/READ | Recovery Services-tároló konfigurációjának visszaadása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/READ | Biztonsági másolat védelmi szándékának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/Items/READ | Tároló összes elemének beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/backupStatus/Action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/monitoringAlerts/írás | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/operationStatus/READ | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupProtectionIntents/READ | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="billing-reader"></a>Számlázás olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférés engedélyezése a számlázási információkhoz |
> | **Azonosító** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Billing/*/READ | Számlázási adatok olvasása |
> | Microsoft. Commerce/*/READ |  |
> | Microsoft. fogyasztás/*/READ |  |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft. CostManagement/*/READ |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="biztalk-contributor"></a>BizTalk közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. BizTalkServices/BizTalk/* | BizTalk Services-szolgáltatások létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="blockchain-member-node-access-preview"></a>Blockchain-hozzáférés (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz |
> | **Azonosító** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Műveletek** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Blockchain/blockchainMembers/transactionNodes/csatlakozási/művelet | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="blueprint-contributor"></a>Tervezet közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a terv definícióit, de nem rendelheti hozzá őket. |
> | **Azonosító** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Blueprint/tervezetek/* | Terv-definíciók vagy tervrajz-összetevők létrehozása és kezelése. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="blueprint-operator"></a>Blueprint operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új tervrajzokat. Megjegyzés: ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. |
> | **Azonosító** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Blueprint/blueprintAssignments/* | Terv-hozzárendelések létrehozása és kezelése. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cdn-endpoint-contributor"></a>CDN-végpont közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak. |
> | **Azonosító** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. CDN/edgenodes/READ |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profilok/végpontok/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cdn-endpoint-reader"></a>CDN-végpont olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat. |
> | **Azonosító** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. CDN/edgenodes/READ |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profilok/végpontok/*/READ |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cdn-profile-contributor"></a>CDN-profil közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak. |
> | **Azonosító** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. CDN/edgenodes/READ |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profilok/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cdn-profile-reader"></a>CDN-profil olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat. |
> | **Azonosító** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. CDN/edgenodes/READ |  |
> | Microsoft. CDN/operationresults/* |  |
> | Microsoft. CDN/profilok/*/READ |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="classic-network-contributor"></a>Klasszikus hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. ClassicNetwork/* | Klasszikus hálózatok létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="classic-storage-account-contributor"></a>Klasszikus Storage-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. ClassicStorage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban |
> | **Azonosító** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Műveletek** |  |
> | Microsoft. ClassicStorage/storageAccounts/listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft. ClassicStorage/storageAccounts/regeneratekey/művelet | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gép közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
> | **Azonosító** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. ClassicCompute/tartománynév/* | Klasszikus számítási tartománynevek létrehozása és kezelése |
> | Microsoft. ClassicCompute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft. ClassicNetwork/networkSecurityGroups/JOIN/Action |  |
> | Microsoft. ClassicNetwork/reservedIps/link/művelet | Fenntartott IP-cím csatolása |
> | Microsoft. ClassicNetwork/reservedIps/READ | A fenntartott IP-címek beolvasása |
> | Microsoft. ClassicNetwork/virtualNetworks/JOIN/Action | Csatlakozik a virtuális hálózathoz. |
> | Microsoft. ClassicNetwork/virtualNetworks/READ | Szerezze be a virtuális hálózatot. |
> | Microsoft. ClassicStorage/storageAccounts/lemezek/olvasás | A Storage-fiók lemezét adja vissza. |
> | Microsoft. ClassicStorage/storageAccounts/images/READ | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft. ClassicStorage/storageAccounts/READ | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cognitive-services-contributor"></a>Cognitive Services közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. |
> | **Azonosító** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. CognitiveServices/* |  |
> | Microsoft. features/features/READ | Egy előfizetés funkcióinak beolvasása. |
> | Microsoft. features/Providers/features/READ | Egy adott erőforrás-szolgáltatóhoz tartozó előfizetés funkciójának beolvasása. |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft. bepillantások/logDefinitions/olvasás | Napló-definíciók olvasása |
> | Microsoft. bepillantások/metricdefinitions/olvasás | Metrikus definíciók olvasása |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft. Resources/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft. Resources/Subscriptions/resourcegroups/Deployments/* |  |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Adatolvasó (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services-információk olvasását. |
> | **Azonosító** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. CognitiveServices/*/READ |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cognitive-services-user"></a>Cognitive Services felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását. |
> | **Azonosító** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Műveletek** |  |
> | Microsoft. CognitiveServices/*/READ |  |
> | Microsoft. CognitiveServices/accounts/listkeys műveletének beolvasása/Action | Kulcsok listázása |
> | Microsoft. bepillantások/alertRules/olvasás | Klasszikus metrikai riasztás beolvasása |
> | Microsoft. bepillantások/diagnosticSettings/olvasás | Erőforrás diagnosztikai beállításának beolvasása |
> | Microsoft. bepillantások/logDefinitions/olvasás | Napló-definíciók olvasása |
> | Microsoft. bepillantások/metricdefinitions/olvasás | Metrikus definíciók olvasása |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft. Resources/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB fiók-olvasó szerepkör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. |
> | **Azonosító** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések beolvasása, az egyes felhasználók számára megadott engedélyek olvasása |
> | Microsoft. DocumentDB/*/READ | Bármely gyűjtemény beolvasása |
> | Microsoft. DocumentDB/databaseAccounts/readonlykeys/művelet | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Microsoft. bepillantások/MetricDefinitions/olvasás | Metrikus definíciók olvasása |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. |
> | **Azonosító** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Műveletek** |  |
> | Microsoft. DocumentDb/databaseAccounts/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **Nincs tapintat** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Listkeys műveletének beolvasása/* |  |
> | Microsoft. DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz |
> | **Azonosító** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Műveletek** |  |
> | Microsoft. DocumentDB/databaseAccounts/Backup/művelet | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Visszaállítási kérelem elküldése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cost-management-contributor"></a>Cost Management közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) |
> | **Azonosító** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Műveletek** |  |
> | Microsoft. fogyasztás/* |  |
> | Microsoft. CostManagement/* |  |
> | Microsoft. számlázás/billingPeriods/olvasás |  |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Advisor/konfigurációk/olvasás | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="cost-management-reader"></a>Cost Management olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) |
> | **Azonosító** | 72fafb9e-0641-4937-9268 – a91bfd8191a3 |
> | **Műveletek** |  |
> | Microsoft. fogyasztás/*/READ |  |
> | Microsoft. CostManagement/*/READ |  |
> | Microsoft. számlázás/billingPeriods/olvasás |  |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Advisor/konfigurációk/olvasás | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="data-box-contributor"></a>Data Box közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. |
> | **Azonosító** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Databox/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="data-box-reader"></a>Data Box olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. |
> | **Azonosító** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Databox/*/READ |  |
> | Microsoft. Databox/Jobs/listsecrets/művelet |  |
> | Microsoft. Databox/Jobs/listcredentials/művelet | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | Microsoft. Databox/Locations/availableSkus/Action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | Microsoft. Databox/Locations/validateAddress/Action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="data-factory-contributor"></a>Data Factory közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. |
> | **Azonosító** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. DataFactory/dataFactories/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft. DataFactory/gyárak/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics fejlesztő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. |
> | **Azonosító** | 47b7735b-770E-4598-a7da-8b91488b4c88 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. BigAnalytics/fiókok/* |  |
> | Microsoft. DataLakeAnalytics/fiókok/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. BigAnalytics/fiókok/törlés |  |
> | Microsoft. BigAnalytics/accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/fiókok/írás |  |
> | Microsoft. DataLakeAnalytics/fiókok/törlés | DataLakeAnalytics-fiók törlése. |
> | Microsoft. DataLakeAnalytics/accounts/TakeOwnership/Action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Microsoft. DataLakeAnalytics/fiókok/írás | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Microsoft. DataLakeAnalytics/fiókok/dataLakeStoreAccounts/írás | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | Microsoft. DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | Microsoft. DataLakeAnalytics/fiókok/storageAccounts/írás | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | Microsoft. DataLakeAnalytics/accounts/storageAccounts/delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | Microsoft. DataLakeAnalytics/fiókok/firewallRules/írás | Tűzfalszabály létrehozása vagy frissítése. |
> | Microsoft. DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Microsoft. DataLakeAnalytics/fiókok/computePolicies/írás | Számítási szabályzat létrehozása vagy frissítése. |
> | Microsoft. DataLakeAnalytics/accounts/computePolicies/delete | Számítási szabályzat törlése. |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="data-purger"></a>Adattisztító
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Törölheti az elemzési adatfájlokat |
> | **Azonosító** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Műveletek** |  |
> | Microsoft. bepillantások/összetevők/*/READ |  |
> | Microsoft. bepillantások/összetevők/kiürítés/művelet | Adatok törlése a Application Insightsból |
> | Microsoft. OperationalInsights/munkaterületek/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="devtest-labs-user"></a>DevTest Labs-felhasználó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását. |
> | **Azonosító** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. számítás/availabilitySets/olvasás | Rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Microsoft. számítási/virtualMachines/*/READ | Virtuális gép tulajdonságainak beolvasása (virtuálisgép-méretek, futásidejű állapot, virtuálisgép-bővítmények stb.) |
> | Microsoft. számítás/virtualMachines/felszabadítás/művelet | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | Microsoft. számítás/virtualMachines/olvasás | Virtuális gép tulajdonságainak beolvasása |
> | Microsoft. számítás/virtualMachines/újraindítás/művelet | Újraindítja a virtuális gépet |
> | Microsoft. számítás/virtualMachines/indítás/művelet | Elindítja a virtuális gépet |
> | Microsoft. segédösszetevője/*/READ | Tesztkörnyezet tulajdonságainak olvasása |
> | Microsoft. segédösszetevője/Labs/claimAnyVm/művelet | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | Microsoft. segédösszetevője/Labs/createEnvironment/művelet | Virtuális gépek létrehozása tesztkörnyezetben. |
> | Microsoft. segédösszetevője/Labs/ensureCurrentUserProfile/művelet | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | Microsoft. segédösszetevője/Labs/képletek/törlés | Képletek törlése. |
> | Microsoft. segédösszetevője/Labs/képletek/olvasás | Képletek olvasása. |
> | Microsoft. segédösszetevője/Labs/képletek/írás | Képletek hozzáadása vagy módosítása. |
> | Microsoft. segédösszetevője/Labs/policySets/evaluatePolicies/művelet | Kiértékeli a tesztkörnyezet házirendjét. |
> | Microsoft. segédösszetevője/Labs/virtualMachines/jogcím/művelet | Meglévő virtuális gép tulajdonjogának átvétele |
> | Microsoft. segédösszetevője/Labs/virtualmachines/listApplicableSchedules/művelet | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Microsoft. segédösszetevője/Labs/virtualMachines/getRdpFileContents/művelet | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | Microsoft. Network/loadBalancers/backendAddressPools/JOIN/Action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Microsoft. Network/loadBalancers/inboundNatRules/JOIN/Action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Microsoft. Network/networkInterfaces/*/READ | Olvassa el egy hálózati adapter tulajdonságait (például az összes olyan terheléselosztó, amely a hálózati adapter részét képezi) |
> | Microsoft. Network/networkInterfaces/csatlakozás/művelet | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | Microsoft. Network/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft. Network/networkInterfaces/Write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Microsoft. Network/nyilvános IP/*/READ | Nyilvános IP-cím tulajdonságainak beolvasása |
> | Microsoft. Network/nyilvános IP/csatlakozás/művelet | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Microsoft. Network/nyilvános IP/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft. Resources/központi telepítések/olvasás | Lekérdezi vagy felsorolja az üzemelő példányokat. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | **Nincs tapintat** |  |
> | Microsoft. számítás/virtualMachines/méreteinek listáján/olvasás | A virtuális gép által frissíthető elérhető méretek listája |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="dns-zone-contributor"></a>DNS-zóna közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk. |
> | **Azonosító** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/dnsZones/* | DNS-zónák és-rekordok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB. |
> | **Azonosító** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. DocumentDb/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését. |
> | **Azonosító** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/READ | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Microsoft. EventGrid/Locations/eventSubscriptions/READ | Regionális esemény-előfizetések listázása |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/READ | Regionális esemény-előfizetések listázása topictype szerint |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a EventGrid esemény-előfizetések olvasását. |
> | **Azonosító** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. EventGrid/eventSubscriptions/READ | EventSubscription beolvasása |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/READ | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Microsoft. EventGrid/Locations/eventSubscriptions/READ | Regionális esemény-előfizetések listázása |
> | Microsoft. EventGrid/Locations/topicTypes/eventSubscriptions/READ | Regionális esemény-előfizetések listázása topictype szerint |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-fürt operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását. |
> | **Azonosító** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Műveletek** |  |
> | Microsoft. HDInsight/*/READ |  |
> | Microsoft. HDInsight/fürtök/getGatewaySettings/művelet | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | Microsoft. HDInsight/fürtök/updateGatewaySettings/művelet | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | Microsoft. HDInsight/fürtök/konfigurációk/* |  |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight tartományi szolgáltatások közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package |
> | **Azonosító** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Műveletek** |  |
> | Microsoft. HRE/*/READ |  |
> | Microsoft. HRE/domainServices/*/READ |  |
> | Microsoft. HRE/domainServices/oucontainer/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligens rendszer-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. IntelligentSystems/fiókok/* | Intelligens rendszerek fiókjainak létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="key-vault-contributor"></a>Key Vault közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a kulcstartók kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. kulcstartó/* |  |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. kulcstartó/Locations/deletedVaults/Purge/művelet | Helyreállítható törölhető kulcstartó törlése |
> | Microsoft. kulcstartó/hsmPools/* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

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
> | Microsoft. LabServices/labAccounts/sizes/getRegionalAvailability/Action |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/művelet | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="log-analytics-contributor"></a>Log Analytics közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz. |
> | **Azonosító** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft. ClassicCompute/virtualMachines/Extensions/* |  |
> | Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft. számítás/virtualMachines/Extensions/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft. OperationalInsights/* |  |
> | Microsoft. OperationsManagement/* |  |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourcegroups/Deployments/* |  |
> | Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="log-analytics-reader"></a>Log Analytics olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
> | **Azonosító** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motor használatával. |
> | Microsoft. OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft. OperationalInsights/munkaterületek/sharedKeys/olvasás | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést. |
> | **Azonosító** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. ClassicStorage/storageAccounts/Listkeys műveletének beolvasása/művelet | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft. ClassicStorage/storageAccounts/READ | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/metricAlerts/* |  |
> | Microsoft. bepillantások/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft. bepillantások/logdefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | Microsoft. bepillantások/metricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | Microsoft. Logic/* | Felügyeli Logic Apps erőforrásait. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Web/connectionGateways/* | Hozzon létre és kezelje a kapcsolatok átjáróját. |
> | Microsoft. Web/Connections/* | Hozzon létre és kezelje a kapcsolatokat. |
> | Microsoft. Web/customApis/* | Egyéni API létrehozása és kezelése. |
> | Microsoft. Web/kiszolgálófarmok/csatlakozás/művelet |  |
> | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | Microsoft. Web/Sites/functions/listSecrets/Action | A függvények titkainak listázása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="logic-app-operator"></a>Logic app-operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket. |
> | **Azonosító** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/*/READ | Az információk beolvasása riasztási szabályok |
> | Microsoft. bepillantások/metricAlerts/*/READ |  |
> | Microsoft. bepillantások/diagnosticSettings/*/READ | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft. bepillantások/metricDefinitions/*/READ | A Logic Apps elérhető metrikáinak beolvasása. |
> | Microsoft. Logic/*/READ | Logic Apps erőforrások beolvasása. |
> | Microsoft. Logic/munkafolyamatok/letiltás/művelet | Letiltja a munkafolyamatot. |
> | Microsoft. Logic/munkafolyamatok/engedélyezés/művelet | A munkafolyamat engedélyezése. |
> | Microsoft. Logic/munkafolyamatok/érvényesítés/művelet | Ellenőrzi a munkafolyamatot. |
> | Microsoft. Resources/központi telepítések/műveletek/olvasás | Lekérdezi vagy felsorolja az üzembe helyezési műveleteket. |
> | Microsoft. Resources/Subscriptions/operationresults/READ | Az előfizetési művelet eredményeinek beolvasása. |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Web/connectionGateways/*/READ | A kapcsolatok átjáróinak beolvasása. |
> | Microsoft. Web/Connections/*/READ | Olvasási kapcsolatok. |
> | Microsoft. Web/customApis/*/READ | Egyéni API olvasása. |
> | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain |
> | **Azonosító** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Solutions/alkalmazások/olvasás | Az alkalmazások listájának beolvasása. |
> | Microsoft. Solutions/*/Action |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="managed-applications-reader"></a>Felügyelt alkalmazások olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. |
> | **Azonosító** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Solutions/jitRequests/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése |
> | **Azonosító** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Műveletek** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/READ | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | Microsoft. ManagedIdentity/userAssignedIdentities/Write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |
> | Microsoft. ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="managed-identity-operator"></a>Felügyelt identitás operátora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználóhoz rendelt identitás olvasása és hozzárendelése |
> | **Azonosító** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Műveletek** |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/READ |  |
> | Microsoft. ManagedIdentity/userAssignedIdentities/*/assign/Action |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését. |
> | **Azonosító** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | **Műveletek** |  |
> | Microsoft. ManagedServices/registrationAssignments/READ | A felügyelt szolgáltatások regisztrációs hozzárendeléseinek listáját kéri le. |
> | Microsoft. ManagedServices/registrationAssignments/delete | Eltávolítja a felügyelt szolgáltatások regisztrációjának hozzárendelését. |
> | Microsoft. ManagedServices/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="management-group-contributor"></a>Felügyeleti csoport közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport közreműködői szerepköre |
> | **Azonosító** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Műveletek** |  |
> | Microsoft. Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft. Management/managementGroups/előfizetések/törlés | Az előfizetés társítása a felügyeleti csoportból. |
> | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="management-group-reader"></a>Felügyeleti csoport olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felügyeleti csoport olvasójának szerepköre |
> | **Azonosító** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Műveletek** |  |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="monitoring-contributor"></a>Közreműködő figyelése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. AlertsManagement/riasztások/* |  |
> | Microsoft. AlertsManagement/alertsSummary/* |  |
> | Microsoft. bepillantások/actiongroups/* |  |
> | Microsoft. bepillantások/activityLogAlerts/* |  |
> | Microsoft. bepillantások/AlertRules/* | Riasztási szabályok olvasása/írása/törlése. |
> | Microsoft. bepillantások/összetevők/* | Application Insights összetevők olvasása/írása/törlése. |
> | Microsoft. bepillantások/DiagnosticSettings/* | Diagnosztikai beállítások olvasása/írása/törlése. |
> | Microsoft. bepillantások/eventtypes/* | Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában. |
> | Microsoft. bepillantások/LogDefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | Microsoft. bepillantások/metricalerts/* |  |
> | Microsoft. bepillantások/MetricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | Microsoft. bepillantások/mérőszámok/* | Erőforrás metrikáinak olvasása. |
> | Microsoft. elemzések/regisztráció/művelet | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft. bepillantások/scheduledqueryrules/* |  |
> | Microsoft. bepillantások/webteszt/* | Application Insights webes tesztek olvasása/írása/törlése. |
> | Microsoft. bepillantások/munkafüzetek/* |  |
> | Microsoft. OperationalInsights/munkaterületek/intelligencepacks/* | Log Analytics-megoldási csomagok olvasása/írása/törlése. |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/* | A log Analytics-beli mentett keresések olvasása/írása/törlése. |
> | Microsoft. OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft. OperationalInsights/munkaterületek/sharedKeys/művelet | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/* | A log Analytics tárolási Insight-konfigurációinak olvasása/írása/törlése. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. WorkloadMonitor/figyelők/* |  |
> | Microsoft. WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét |
> | **Azonosító** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Műveletek** |  |
> | Microsoft. elemzések/regisztráció/művelet | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. bepillantások/mérőszámok/írás | Mérőszámok írása |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="monitoring-reader"></a>Figyelő olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Azonosító** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="network-contributor"></a>Hálózati közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/* | Hálózatok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="new-relic-apm-account-contributor"></a>Új ereklye APM-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | NewRelic. APM/fiókok/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="policy-insights-data-writer-preview"></a>Házirend-elemzések adatírója (előzetes verzió)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez. |
> | **Azonosító** | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | **Műveletek** |  |
> | Microsoft. Authorization/policyassignments/olvasás | Szabályzat-hozzárendelés adatainak beolvasása. |
> | Microsoft. Authorization/policydefinitions/olvasás | Házirend-definíció adatainak beolvasása. |
> | Microsoft. Authorization/policysetdefinitions/olvasás | Házirend-készlet definíciójának beolvasása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. PolicyInsights/checkDataPolicyCompliance/művelet | Egy adott összetevő megfelelőségi állapotának ellenőrzése adatszabályzatok szerint. |
> | Microsoft. PolicyInsights/policyEvents/logDataEvents/művelet | Naplózza az erőforrás-összetevő házirendjének eseményeit. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="reader-and-data-access"></a>Olvasó és adathozzáférés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. |
> | **Azonosító** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/művelet | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="redis-cache-contributor"></a>Redis Cache közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. cache/Redis/* | Redis cache-gyorsítótárak létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="resource-policy-contributor"></a>Erőforrás-szabályzat közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók. |
> | **Azonosító** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Authorization/policyassignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft. Authorization/policydefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft. Authorization/policysetdefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="scheduler-job-collections-contributor"></a>Ütemező – feladattípusok közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. |
> | **Azonosító** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Scheduler/feladatgyűjtemények/* | Feladatok gyűjteményének létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="search-service-contributor"></a>Search Service közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Search/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="security-admin"></a>Biztonsági rendszergazda
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak Security Center esetén: megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, biztonsági házirendeket szerkeszthet, megtekintheti a riasztásokat és a javaslatokat |
> | **Azonosító** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Authorization/policyAssignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft. Authorization/policyDefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft. Authorization/policySetDefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft. operationalInsights/munkaterületek/*/READ | Log Analytics-adatértékek megtekintése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Security/* |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="security-manager-legacy"></a>Security Manager (örökölt)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát |
> | **Azonosító** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. ClassicCompute/*/READ | Konfigurációs információk beolvasása a klasszikus virtuális gépeken |
> | Microsoft. ClassicCompute/virtualMachines/*/Write | A klasszikus virtuális gépek írási konfigurációja |
> | Microsoft. ClassicNetwork/*/READ | A klasszikus hálózat konfigurációs információinak olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="security-reader"></a>Biztonsági olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Csak Security Center esetén: megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat |
> | **Azonosító** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. operationalInsights/munkaterületek/*/READ | Log Analytics-adatértékek megtekintése |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Security/*/READ | Biztonsági összetevők és szabályzatok olvasása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Management/managementGroups/olvasás | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="site-recovery-contributor"></a>Site Recovery közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést |
> | **Azonosító** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocateStamp/Action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/tanúsítványok/írás | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/* | A tár szolgáltatással kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/refreshContainers/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationAlertSettings/* | Replikációs riasztási beállítások létrehozása vagy frissítése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationEvents/READ | Események beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/* | Replikációs hálók létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/* | Replikációs házirendek létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/* | Helyreállítási tervek létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/storageConfig/* | Recovery Services-tároló tárolási konfigurációjának létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/tokenInfo/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/notificationConfiguration/READ |  |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationOperationStatus/READ | A tár replikációs művelet állapotának beolvasása |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="site-recovery-operator"></a>Site Recovery operátor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket |
> | **Azonosító** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocateStamp/Action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/refreshContainers/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationAlertSettings/READ | Riasztási beállítások beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationEvents/READ | Események beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/checkConsistency/művelet | A háló konzisztenciájának ellenőrzése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/READ | Bármilyen háló beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/reassociateGateway/művelet | Átjáró újrahozzárendelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/renewcertificate/művelet | Háló tanúsítványának megújítása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/READ | Bármely védhető elem olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/művelet | Helyreállítási pont alkalmazása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/művelet | Feladatátvétel véglegesítve |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/művelet | Tervezett feladatátvétel |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/READ | Védett elemek beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/READ | Replikációs helyreállítási pontok olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/művelet | Replikáció javítása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ismételt védelem/művelet | Védett elemek ismételt védelme |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/switchprotection/művelet | Védelmi tároló váltása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/művelet | Feladatátvételi teszt |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/művelet | Feladatátvételi teszt karbantartása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/művelet | Feladatátvétel |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/művelet | Mobilitási szolgáltatás frissítése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/művelet | Szolgáltató frissítése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/READ | Bármilyen szabályzat beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/failoverCommit/művelet | Feladatátvételi véglegesítés helyreállítási terve |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/plannedFailover/művelet | Tervezett feladatátvétel helyreállítási terve |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/READ | Helyreállítási tervek olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/replicationRecoveryPlans/ismételt védelem/művelet | Helyreállítási terv ismételt védetté |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/testFailover/művelet | Feladatátvételi teszt helyreállítási terve |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/testFailoverCleanup/művelet | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/replicationRecoveryPlans/unplannedFailover/művelet | Feladatátvétel helyreállítási terve |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/notificationConfiguration/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/storageConfig/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/tokenInfo/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="site-recovery-reader"></a>Site Recovery olvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket |
> | **Azonosító** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/allocatedStamp/READ | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/extendedInformation/READ | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringAlerts/READ | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/monitoringConfigurations/notificationConfiguration/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/refreshContainers/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/operationResults/READ | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/registeredIdentities/READ | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationAlertSettings/READ | Riasztási beállítások beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationEvents/READ | Események beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/READ | Bármilyen háló beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/READ | Bármilyen hálózat beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationNetworks/replicationNetworkMappings/READ | Hálózati leképezések olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/READ | Védelmi tárolók beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/READ | Bármely védhető elem olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/READ | Védett elemek beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/READ | Replikációs helyreállítási pontok olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/READ | A védelmi tárolók megfeleltetésének beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationRecoveryServicesProviders/READ | Recovery Services szolgáltatók beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/READ | A tárolási besorolások beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/READ | A tárolási besorolási leképezések beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationFabrics/replicationvCenters/READ | Bármilyen vCenter beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationJobs/READ | Bármilyen feladat beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationPolicies/READ | Bármilyen szabályzat beolvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationRecoveryPlans/READ | Helyreállítási tervek olvasása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/storageConfig/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/tokenInfo/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/vaultTokens/READ | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="spatial-anchors-account-contributor"></a>Térbeli horgonyok fiók közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket |
> | **Azonosító** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/létrehozás/művelet | Térbeli horgonyok létrehozása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/tulajdonságok/olvasás | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiókjának tulajdonosa
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is |
> | **Azonosító** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/létrehozás/művelet | Térbeli horgonyok létrehozása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/tulajdonságok/olvasás | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiókjának olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását |
> | **Azonosító** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/tulajdonságok/olvasás | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/READ | Térbeli horgonyok keresése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/READ | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="sql-db-contributor"></a>SQL-adatbázis közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. |
> | **Azonosító** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. SQL/Locations/*/READ |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/* | SQL-adatbázisok létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | Microsoft. SQL/managedInstances/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingPolicies/* | Naplózási házirendek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingSettings/* | Naplózási beállítások szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditRecords/olvasás | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | Microsoft. SQL/kiszolgálók/adatbázisok/connectionPolicies/* | A kapcsolatok házirendjeinek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/* | Adatmaszkolási házirendek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/extendedAuditingSettings/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityAlertPolicies/* | Biztonsági riasztási házirendek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityMetrics/* | Biztonsági mérőszámok szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/kiszolgálók/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="sql-managed-instance-contributor"></a>SQL felügyelt példány közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. |
> | **Azonosító** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Műveletek** |  |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft. SQL/Locations/*/READ |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/Subnets/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="sql-security-manager"></a>SQL Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Microsoft-hitelesítés olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. SQL/managedInstances/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/transparentDataEncryption/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/auditingPolicies/* | SQL Server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/auditingSettings/* | SQL Server naplózási beállításának létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/extendedAuditingSettings/olvasás | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingPolicies/* | SQL Server-adatbázis naplózási házirendjeinek létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditRecords/olvasás | Naplózási rekordok olvasása |
> | Microsoft. SQL/kiszolgálók/adatbázisok/connectionPolicies/* | SQL Server adatbázis-kapcsolatok házirendjeinek létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/extendedAuditingSettings/olvasás | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | Microsoft. SQL/kiszolgálók/adatbázisok/olvasás | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/olvasás | Adatbázis-séma beszerzése. |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/olvasás | Adatbázis oszlopának beolvasása. |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/olvasás | Adatbázis táblázatának beolvasása. |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/transparentDataEncryption/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/kiszolgálók/firewallRules/* |  |
> | Microsoft. SQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Microsoft. SQL/kiszolgálók/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft. SQL/kiszolgálók/vulnerabilityAssessments/* |  |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="sql-server-contributor"></a>SQL Server közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. |
> | **Azonosító** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. SQL/Locations/*/READ |  |
> | Microsoft. SQL/kiszolgálók/* | SQL-kiszolgálók létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. bepillantások/mérőszámok/olvasás | Metrikák olvasása |
> | Microsoft. bepillantások/metricDefinitions/olvasás | Metrikus definíciók olvasása |
> | **Nincs tapintat** |  |
> | Microsoft. SQL/managedInstances/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/auditingPolicies/* | SQL Server naplózási szabályzatok szerkesztése |
> | Microsoft. SQL/kiszolgálók/auditingSettings/* | Az SQL Server naplózási beállításainak szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingPolicies/* | SQL Server-adatbázis naplózási házirendjeinek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/auditRecords/olvasás | Naplózási rekordok olvasása |
> | Microsoft. SQL/kiszolgálók/adatbázisok/connectionPolicies/* | SQL Server adatbázis-kapcsolatok házirendjeinek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/currentSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/dataMaskingPolicies/* | Az SQL Server-adatbázis adatmaszkolási házirendjeinek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/extendedAuditingSettings/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sémák/táblák/oszlopok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityAlertPolicies/* | SQL Server-adatbázis biztonsági riasztási házirendjeinek szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/securityMetrics/* | SQL Server-adatbázis biztonsági metrikáinak szerkesztése |
> | Microsoft. SQL/kiszolgálók/adatbázisok/sensitivityLabels/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentScans/* |  |
> | Microsoft. SQL/kiszolgálók/adatbázisok/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/kiszolgálók/extendedAuditingSettings/* |  |
> | Microsoft. SQL/kiszolgálók/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok szerkesztése |
> | Microsoft. SQL/kiszolgálók/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-account-contributor"></a>Storage-fiók közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. |
> | **Azonosító** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Az összes engedély olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/diagnosticSettings/* | Diagnosztikai beállítások kezelése |
> | Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/művelet | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-account-key-operator-service-role"></a>A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. |
> | **Azonosító** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft. Storage/storageAccounts/regeneratekey/művelet | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-blob-data-contributor"></a>Storage blob adatközreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/olvasás | Tároló vagy tárolók listájának visszaadása. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/írás | Egy tároló metaadatainak vagy tulajdonságainak módosítása. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/művelet | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/delete | BLOB törlése. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/olvasás | BLOB vagy Blobok listájának visszaadása. |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/írás | Írás blobba. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-blob-data-owner"></a>Storage blob-adattulajdonos
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/* | A tárolók teljes engedélyei. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/művelet | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/containers/Blobok/* | A Blobok teljes engedélyei. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-blob-data-reader"></a>Storage blob-Adatolvasó
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/olvasás | Tároló vagy tárolók listájának visszaadása. |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/művelet | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/tárolók/Blobok/olvasás | BLOB vagy Blobok listájának visszaadása. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-blob-delegator"></a>Storage blob-delegáló
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)című témakörben talál. |
> | **Azonosító** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey/művelet | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Storage file-adatsmb-megosztás közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül |
> | **Azonosító** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/Write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage file-adatsmb-megosztás emelt szintű közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási, írási, törlési és módosítási engedélyek engedélyezése az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli eléréséhez |
> | **Azonosító** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/Write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/modifypermissions/művelet | Egy fájlra vagy mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-file-data-smb-share-reader"></a>Storage-fájl adatsmb-megosztásának olvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás SMB használatával |
> | **Azonosító** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/fileServices/állnak/Files/READ | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-queue-data-contributor"></a>Tárolási várólista adatközreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/delete | Várólista törlése. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/READ | Várólista vagy várólisták listájának visszaadása. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Várólista metaadatainak vagy tulajdonságainak módosítása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/delete | Egy vagy több üzenet törlése egy várólistából. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/READ | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor adatüzenet-processzora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/READ | Üzenet bepillantása. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Üzenet beolvasása és törlése. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-queue-data-message-sender"></a>Tárolási várólista adatüzenetének küldője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Hozzáadás/művelet | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="storage-queue-data-reader"></a>Storage-várólista adatolvasója
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Azonosító** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Műveletek** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/READ | Várólistát vagy várólisták listáját adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/READ | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="support-request-contributor"></a>Támogatási kérelem közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a támogatási kérések létrehozását és kezelését |
> | **Azonosító** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager közreműködő
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását. |
> | **Azonosító** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/trafficManagerProfiles/* |  |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. |
> | **Azonosító** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Authorization/* | Engedélyezés kezelése |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="virtual-machine-administrator-login"></a>Virtuális gép rendszergazdai bejelentkezése
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként |
> | **Azonosító** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Műveletek** |  |
> | Microsoft. Network/nyilvános IP/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Network/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | Microsoft. Network/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft. számítási/virtualMachines/*/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. számítás/virtualMachines/bejelentkezés/művelet | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | Microsoft. számítás/virtualMachines/loginAsAdmin/művelet | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak. |
> | **Azonosító** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. számítási/availabilitySets/* | Számítási rendelkezésre állási csoportok létrehozása és kezelése |
> | Microsoft. számítás/helyszínek/* | Számítási helyszínek létrehozása és kezelése |
> | Microsoft. számítási/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft. számítási/virtualMachineScaleSets/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft. segédösszetevője/Schedules/* |  |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Network/applicationGateways/backendAddressPools/JOIN/Action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | Microsoft. Network/loadBalancers/backendAddressPools/JOIN/Action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Microsoft. Network/loadBalancers/inboundNatPools/JOIN/Action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | Microsoft. Network/loadBalancers/inboundNatRules/JOIN/Action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Microsoft. Network/loadBalancers/Szondák/csatlakozás/művelet | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | Microsoft. Network/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | Microsoft. Network/Locations/* | Hálózati telephelyek létrehozása és kezelése |
> | Microsoft. Network/networkInterfaces/* | Hálózati adapterek létrehozása és kezelése |
> | Microsoft. Network/networkSecurityGroups/csatlakozás/művelet | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Microsoft. Network/networkSecurityGroups/READ | Hálózati biztonsági csoport definíciójának beolvasása |
> | Microsoft. Network/nyilvános IP/csatlakozás/művelet | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Microsoft. Network/nyilvános IP/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Network/virtualNetworks/alhálózatok/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Microsoft. Recoveryservices szolgáltatónál/Locations/* |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/Write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/*/READ |  |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/READ | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/Write | Védett elemek biztonsági másolatának létrehozása |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/backupPolicies/írás | Védelmi szabályzat létrehozása |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/tárolók/írás | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/Listkeys műveletének beolvasása/művelet | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft. Storage/storageAccounts/olvasás | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezés
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként. |
> | **Azonosító** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Műveletek** |  |
> | Microsoft. Network/nyilvános IP/READ | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft. Network/virtualNetworks/READ | A virtuális hálózat definíciójának beolvasása |
> | Microsoft. Network/loadBalancers/READ | Terheléselosztó definíciójának beolvasása |
> | Microsoft. Network/networkInterfaces/READ | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft. számítási/virtualMachines/*/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. számítás/virtualMachines/bejelentkezés/művelet | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="web-plan-contributor"></a>Webes csomag közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül. |
> | **Azonosító** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Web/kiszolgálófarmok/* | Kiszolgálófarm létrehozása és kezelése |
> | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="website-contributor"></a>Webhely közreműködői
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Leírás** | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk. |
> | **Azonosító** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Olvasási engedély |
> | Microsoft. bepillantások/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/összetevők/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft. ResourceHealth/availabilityStatuses/READ | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft. Resources/üzemelő példány/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/READ | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft. support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Web/tanúsítványok/* | Webhely-tanúsítványok létrehozása és kezelése |
> | Microsoft. Web/listSitesAssignedToHostName/READ | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | Microsoft. Web/kiszolgálófarmok/csatlakozás/művelet |  |
> | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | Microsoft. Web/Sites/* | Webhelyek létrehozása és kezelése (a hely létrehozásához írási engedély szükséges a társított App Service csomaghoz) |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
