---
title: Azure-beli beépített szerepkörök az Azure RBAC
description: Ismerteti az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Azure-beli beépített szerepköreit. Felsorolja a műveleteket, a nem Tapintatokat, a DataActions és a NotDataActions.
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
ms.date: 02/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b7b944880074b64f6a9e66e177082e52632e9c9d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205891"
---
# <a name="azure-built-in-roles"></a>Azure beépített szerepkörök

Az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](overview.md) számos olyan Azure-beli beépített szerepkört tartalmaz, amelyeket a felhasználókhoz, csoportokhoz, egyszerű szolgáltatásokhoz és felügyelt identitásokhoz rendelhet hozzá. A szerepkör-hozzárendelések lehetővé teszik az Azure-erőforrásokhoz való hozzáférés szabályozását. Ha a beépített szerepkörök nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját [Egyéni Azure-szerepköröket](custom-roles.md)is.

Ez a cikk az Azure-erőforrások beépített szerepköreit sorolja fel, amelyek folyamatosan fejlődnek. A legújabb szerepkörök beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget. Ha a Azure Active Directory rendszergazdai szerepköreit keresi, tekintse meg a [rendszergazdai szerepkör engedélyeit Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="descriptions-and-ids"></a>Leírások és azonosítók

Az alábbi táblázat egy rövid leírást és az egyes beépített szerepkörök egyedi AZONOSÍTÓját tartalmazza. A szerepkör nevére kattintva megtekintheti az egyes szerepkörökhöz tartozó `Actions`, `NotActions`, `DataActions`és `NotDataActions` listáját. További információ ezekről a műveletekről, valamint a kezelési és adatsíkokra való alkalmazásáról: az [Azure-erőforrások szerepkör-definícióinak megismerése](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Beépített szerepkör | Leírás | ID (Azonosító) |
> | --- | --- | --- |
> | [AcrDelete](#acrdelete) | ACR törlése | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-rendszerkép aláírója | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR-lekérés | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR leküldése | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR karanténba helyezési Adatolvasó | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | az ACR karanténba helyezési adatírója | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [API Management szolgáltatás közreműködői](#api-management-service-contributor) | Képes a szolgáltatás és az API-k kezelésére | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API Management Service operátori szerepkör](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de nem az API-kat | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API Management szolgáltatás-olvasó szerepkör](#api-management-service-reader-role) | Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Alkalmazás-konfigurációs adattulajdonos](#app-configuration-data-owner) | Lehetővé teszi az alkalmazás konfigurációs adatának teljes hozzáférését. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Az alkalmazás konfigurációs adatolvasója](#app-configuration-data-reader) | Olvasási hozzáférés engedélyezése az alkalmazás konfigurációs adatfájljaihoz. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Application Insights összetevő közreműködője](#application-insights-component-contributor) | Felügyelheti Application Insights összetevőket | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Automation-feladatok operátora](#automation-job-operator) | Feladatok létrehozása és kezelése Automation-Runbookok használatával. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operátor](#automation-operator) | Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook operátor](#automation-runbook-operator) | A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Avere közreműködője](#avere-contributor) | Létrehozhat és kezelhet egy avere vFXT-fürtöt. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere operátor](#avere-operator) | A avere vFXT-fürt által használt fürt kezelése | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Azure-beli csatlakoztatott gépek bevezetése](#azure-connected-machine-onboarding) | Az Azure-beli csatlakoztatott gépeket is képes bevezetni. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure-beli csatlakoztatott számítógép erőforrás-rendszergazdája](#azure-connected-machine-resource-administrator) | Képes olvasni, írni, törölni és újratelepíteni az Azure-beli csatlakoztatott gépeket. | cd570a14-e51a-42ad-bac8-bafd67325302 |
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
> | [Közreműködő](#contributor) | Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférés biztosítását. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Cosmos DB fiók-olvasó szerepkör](#cosmos-db-account-reader-role) | Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operátor](#cosmos-db-operator) | Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Cost Management közreműködő](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management olvasó](#cost-management-reader) | Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Data Box közreműködő](#data-box-contributor) | A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box olvasó](#data-box-reader) | Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Factory közreműködő](#data-factory-contributor) | Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Lake Analytics fejlesztő](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
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
> | [Felügyelt alkalmazás közreműködői szerepköre](#managed-application-contributor-role) | Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Felügyelt alkalmazás operátori szerepköre](#managed-application-operator-role) | Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Felügyelt identitás közreműködői](#managed-identity-contributor) | Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Felügyelt identitás operátora](#managed-identity-operator) | Felhasználóhoz rendelt identitás olvasása és hozzárendelése | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre](#managed-services-registration-assignment-delete-role) | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Felügyeleti csoport közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködői szerepköre | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Felügyeleti csoport olvasója](#management-group-reader) | Felügyeleti csoport olvasójának szerepköre | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Közreműködő figyelése](#monitoring-contributor) | Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Figyelési metrikák közzétevője](#monitoring-metrics-publisher) | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Figyelő olvasó](#monitoring-reader) | Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Új ereklye APM-fiók közreműködői](#new-relic-apm-account-contributor) | Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Tulajdonos](#owner) | Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Házirend-elemzések adatírója (előzetes verzió)](#policy-insights-data-writer-preview) | Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Olvasó és adathozzáférés](#reader-and-data-access) | Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Redis Cache közreműködő](#redis-cache-contributor) | Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Erőforrás-házirend közreműködője](#resource-policy-contributor) | Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Ütemező – feladattípusok közreműködői](#scheduler-job-collections-contributor) | Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Search Service közreműködő](#search-service-contributor) | Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Biztonsági rendszergazda](#security-admin) | Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és a javaslatokat, valamint | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Biztonsági értékelő közreműködő](#security-assessment-contributor) | Lehetővé teszi az értékelések leküldését Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Security Manager (örökölt)](#security-manager-legacy) | Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Biztonsági olvasó](#security-reader) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Site Recovery közreműködő](#site-recovery-contributor) | Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery olvasó](#site-recovery-reader) | Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Térbeli horgonyok fiók közreműködője](#spatial-anchors-account-contributor) | Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Térbeli horgonyok fiókjának tulajdonosa](#spatial-anchors-account-owner) | Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Térbeli horgonyok fiókjának olvasója](#spatial-anchors-account-reader) | Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [SQL-adatbázis közreműködői](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL felügyelt példány közreműködője](#sql-managed-instance-contributor) | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server közreműködő](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Tárfiók-közreműködő](#storage-account-contributor) | Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre](#storage-account-key-operator-service-role) | Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage blob adatközreműködői](#storage-blob-data-contributor) | Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage blob-adattulajdonos](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage blob-Adatolvasó](#storage-blob-data-reader) | Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage blob-delegáló](#storage-blob-delegator) | Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)című témakörben talál. | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Storage file-adatsmb-megosztás közreműködői](#storage-file-data-smb-share-contributor) | Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ehhez a szerepkörhöz nem tartozik a Windows-fájlkiszolgálók beépített megfelelője. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage file-adatsmb-megosztás emelt szintű közreműködője](#storage-file-data-smb-share-elevated-contributor) | Lehetővé teszi az olvasási, írási, törlési és módosítási ACL-eket az Azure-fájlmegosztás fájljain vagy könyvtárain. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók változásának fájlmegosztás ACL-jéhez. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage-fájl adatsmb-megosztásának olvasója](#storage-file-data-smb-share-reader) | Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók olvasásához szükséges fájlmegosztási ACL-vel. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
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
> | [Munkafüzet közreműködője](#workbook-contributor) | Menthetők a megosztott munkafüzetek. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Munkafüzet-olvasó](#workbook-reader) | A munkafüzetek beolvasása. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |


## <a name="general"></a>Általános kérdések


### <a name="contributor"></a>Közreműködő

Lehetővé teszi az összes funkció kezelését, kivéve az erőforrásokhoz való hozzáférés biztosítását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.Authorization/*/Delete | Szerepkörök, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definícióinak törlése |
> | Microsoft.Authorization/*/Write | Szerepkörök, szerepkör-hozzárendelések, szabályzat-hozzárendelések, házirend-definíciók és szabályzat-készlet definíciók létrehozása |
> | Microsoft.Authorization/elevateAccess/Action | A hívó felhasználói hozzáférés rendszergazdai hozzáférésének engedélyezése a bérlői hatókörben |
> | Microsoft. Blueprint/blueprintAssignments/Write | Tervrajz-hozzárendelések létrehozása vagy frissítése |
> | Microsoft. Blueprint/blueprintAssignments/delete | Bármely tervrajz-hozzárendelés törlése |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Tulajdonos

Lehetővé teszi az összes funkció kezelését, beleértve az erőforrásokhoz való hozzáférést is.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Olvasó

Lehetővé teszi, hogy mindent megtekintse, de ne végezzen módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Felhasználói hozzáférés rendszergazdája

Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Authorization/* | Engedélyezés kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI és gépi tanulás


### <a name="cognitive-services-contributor"></a>Cognitive Services közreműködő

Lehetővé teszi Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services Adatolvasó (előzetes verzió)

Lehetővé teszi Cognitive Services-információk olvasását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Cognitive Services felhasználó

Lehetővé teszi Cognitive Services kulcsainak olvasását és listázását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Elemzés


### <a name="azure-event-hubs-data-owner"></a>Az Azure Event Hubs adattulajdonos

Lehetővé teszi az Azure Event Hubs-erőforrások teljes hozzáférését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.EventHub/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs adatfogadó

Engedélyezi az Azure Event Hubs-erőforrások elérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. EventHub/*/eventhubs/consumergroups/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs adatfeladó

Engedélyezi az Azure Event Hubs-erőforrásokhoz való hozzáférés küldését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory közreműködő

Az adatüzemek létrehozása és kezelése, valamint a bennük található alárendelt erőforrások.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DataFactory/dataFactories/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft. DataFactory/gyárak/* | Létrehozhatja és kezelheti az adatelőállítókat és a bennük található alárendelt erőforrásokat. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. EventGrid/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Adattisztító

Törölheti az elemzési adatfájlokat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. bepillantások/összetevők/kiürítés/művelet | Adatok törlése a Application Insightsból |
> | Microsoft. OperationalInsights/munkaterületek/*/READ | Log Analytics-adatértékek megtekintése |
> | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>HDInsight-fürt operátora

Lehetővé teszi a HDInsight-fürt konfigurációjának olvasását és módosítását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>HDInsight tartományi szolgáltatások közreműködője

Elolvashatja, létrehozhatja, módosíthatja és törölheti a HDInsight kapcsolatos tartományi szolgáltatásokat Enterprise Security Package

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics közreműködő

Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics olvasó

Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blokklánc


### <a name="blockchain-member-node-access-preview"></a>Blockchain-hozzáférés (előzetes verzió)

Engedélyezi a hozzáférést a Blockchain-tagok csomópontjaihoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Számítás


### <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gép közreműködője

Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they’re connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Virtuális gép rendszergazdai bejelentkezése

Virtual Machines megtekintése a Portálon és Bejelentkezés rendszergazdaként

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Jelentkezzen be egy virtuális gépre a Windows rendszergazdai vagy a Linux root felhasználói jogosultságokkal |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtuális gépek közreműködője

Lehetővé teszi a virtuális gépek kezelését, de nem fér hozzájuk, nem pedig a virtuális hálózati vagy a Storage-fiókhoz, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/availabilitySets/* | Számítási rendelkezésre állási csoportok létrehozása és kezelése |
> | Microsoft.Compute/locations/* | Számítási helyszínek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachineScaleSets/* | Virtuálisgép-méretezési csoportok létrehozása és kezelése |
> | Microsoft.Compute/disks/write | Új lemez létrehozása vagy egy meglévő frissítése |
> | Microsoft.Compute/disks/read | Lemez tulajdonságainak beolvasása |
> | Microsoft.Compute/disks/delete | A lemez törlése |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Virtuális gép felhasználói bejelentkezés

Tekintse meg Virtual Machines a portálon, és jelentkezzen be normál felhasználóként.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Tárolók


### <a name="acrdelete"></a>AcrDelete

ACR törlése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/artifacts/delete | Összetevő törlése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

ACR-rendszerkép aláírója

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

ACR-lekérés

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

ACR leküldése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Microsoft.ContainerRegistry/registries/push/write | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

ACR karanténba helyezési Adatolvasó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/karanténba helyezés/olvasás | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

az ACR karanténba helyezési adatírója

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. ContainerRegistry/nyilvántartó/karanténba helyezés/olvasás | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Microsoft. ContainerRegistry/nyilvántartások/karanténba helyezés/írás | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes Service-fürt rendszergazdai szerepköre

A fürt rendszergazdai hitelesítő adatainak listázása művelet.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Felügyelt fürt clusterAdmin hitelesítő adatainak listázása |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Felügyelt fürt hozzáférési profiljának beolvasása szerepkör neve alapján a lista hitelesítő adataival |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes Service-fürt felhasználói szerepköre

Fürt felhasználói hitelesítő adatainak listázása.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Felügyelt fürt clusterUser hitelesítő adatainak listázása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Adatbázisok


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB fiók-olvasó szerepkör

Azure Cosmos DB fiókadatok olvasása. Lásd: [DocumentDB Account közreműködő](#documentdb-account-contributor) a Azure Cosmos db-fiókok kezeléséhez.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DocumentDB/*/read | Bármely gyűjtemény beolvasása |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Microsoft.Insights/MetricDefinitions/read | Metrikus definíciók olvasása |
> | Microsoft.Insights/Metrics/read | Metrikák olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB operátor

Lehetővé teszi Azure Cosmos DB-fiókok kezelését, de nem férnek hozzájuk. Megakadályozza a fiók kulcsai és a kapcsolati karakterláncok elérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **Nincs tapintat** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Visszaküldheti a visszaállítási kérelmet egy Cosmos DB adatbázishoz vagy egy fiókhoz tartozó tárolóhoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködői

Felügyelheti Azure Cosmos DB fiókokat. Azure Cosmos DB korábbi nevén DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis Cache közreműködő

Lehetővé teszi a Redis gyorsítótárak kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cache/redis/* | Redis cache-gyorsítótárak létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL-adatbázis közreműködői

Lehetővé teszi az SQL-adatbázisok kezelését, de azokhoz való hozzáférés nélkül. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
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
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL felügyelt példány közreműködője

Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de mások számára nem biztosít hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can’t give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL Security Manager

Lehetővé teszi az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
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
> | Microsoft. SQL/managedInstances/adatbázisok/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL Server naplózási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | SQL Server naplózási beállításának létrehozása és kezelése |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL Server-adatbázis naplózási házirendjeinek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL Server-adatbázis naplózási beállításainak létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázis blob-naplózási rekordjainak beolvasása |
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
> | Microsoft. SQL/kiszolgálók/adatbázisok/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL Server biztonsági riasztási szabályzatok létrehozása és kezelése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server közreműködő

Lehetővé teszi az SQL-kiszolgálók és-adatbázisok kezelését, de azokhoz való hozzáférés nélkül, és nem a biztonsággal kapcsolatos házirendjeiket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázis blob-naplózási rekordjainak beolvasása |
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
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="developer-tools"></a>Fejlesztői eszközök


### <a name="app-configuration-data-owner"></a>Alkalmazás-konfigurációs adattulajdonos

Lehetővé teszi az alkalmazás konfigurációs adatának teljes hozzáférését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/READ |  |
> | Microsoft. AppConfiguration/configurationStores/*/Write |  |
> | Microsoft. AppConfiguration/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Az alkalmazás konfigurációs adatolvasója

Olvasási hozzáférés engedélyezése az alkalmazás konfigurációs adatfájljaihoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. AppConfiguration/configurationStores/*/READ |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Tesztkörnyezet létrehozója

Lehetővé teszi a felügyelt Labs Azure Lab-fiókokban való létrehozását, kezelését és törlését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. LabServices/labAccounts/*/READ |  |
> | Microsoft. LabServices/labAccounts/createLab/művelet | Tesztkörnyezet létrehozása labor-fiókban. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Microsoft. LabServices/labAccounts/getPricingAndAvailability/művelet | A Lab-fiókhoz tartozó méretek, földrajzi és operációs rendszerek kombinációinak díjszabása és rendelkezésre állása. |
> | Microsoft. LabServices/labAccounts/getRestrictionsAndUsage/művelet | Az előfizetéshez tartozó alapvető korlátozások és használat beszerzése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs User

Lehetővé teszi a virtuális gépek a Azure DevTest Labs való összekapcsolását, indítását, újraindítását és leállítását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="hybrid"></a>Hibrid


### <a name="azure-connected-machine-onboarding"></a>Azure-beli csatlakoztatott gépek bevezetése

Az Azure-beli csatlakoztatott gépeket is képes bevezetni.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. HybridCompute/Machines/READ | Bármely Azure-beli ív-gép beolvasása |
> | Microsoft. HybridCompute/gépek/írás | Azure arc-gépek írása |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Vendég-konfiguráció hozzárendelésének beolvasása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure-beli csatlakoztatott számítógép erőforrás-rendszergazdája

Képes olvasni, írni, törölni és újratelepíteni az Azure-beli csatlakoztatott gépeket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. HybridCompute/Machines/READ | Bármely Azure-beli ív-gép beolvasása |
> | Microsoft. HybridCompute/gépek/írás | Azure arc-gépek írása |
> | Microsoft. HybridCompute/Machines/delete | Azure-beli ív-gépek törlése |
> | Microsoft. HybridCompute/gépek/újraösszekapcsolás/művelet | Újrakapcsolódik az Azure arc-gépekhez |
> | Microsoft. HybridCompute/*/READ |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Azure Stack regisztrációs tulajdonos

Lehetővé teszi Azure Stack regisztrációk kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. AzureStack/regisztráció/termékek/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Microsoft.AzureStack/registrations/read | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identitás


### <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködői

Felhasználóhoz rendelt identitás létrehozása, olvasása, frissítése és törlése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Felügyelt identitás operátora

Felhasználóhoz rendelt identitás olvasása és hozzárendelése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integráció


### <a name="api-management-service-contributor"></a>API Management szolgáltatás közreműködői

Képes a szolgáltatás és az API-k kezelésére

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | API Management szolgáltatás létrehozása és kezelése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>API Management Service operátori szerepkör

Kezelheti a szolgáltatást, de nem az API-kat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API Management szolgáltatási példányok beolvasása |
> | Microsoft.ApiManagement/service/backup/action | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Microsoft.ApiManagement/service/delete | API Management szolgáltatás példányának törlése |
> | Microsoft.ApiManagement/service/managedeployments/action | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft.ApiManagement/service/restore/action | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Microsoft.ApiManagement/service/updatecertificate/action | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Microsoft.ApiManagement/service/updatehostname/action | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Microsoft.ApiManagement/service/write | API Management szolgáltatás példányának létrehozása vagy frissítése |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>API Management szolgáltatás-olvasó szerepkör

Csak olvasási hozzáférés a szolgáltatáshoz és az API-khoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API Management szolgáltatási példányok beolvasása |
> | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Azure Service Bus adattulajdonos

Teljes hozzáférés engedélyezése Azure Service Bus erőforrásokhoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Adatfogadó Azure Service Bus

Engedélyezi a hozzáférést Azure Service Bus erőforrásokhoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Adatfeladó Azure Service Bus

Lehetővé teszi a Azure Service Bus erőforrásokhoz való hozzáférés küldését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligens rendszer-fiók közreműködői

Lehetővé teszi az intelligens rendszerfiókok kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.IntelligentSystems/accounts/* | Intelligens rendszerek fiókjainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködői

Lehetővé teszi a logikai alkalmazások kezelését, de nem változtatja meg a hozzájuk való hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a Storage-fiókot az adott fiókkal. |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/metricAlerts/* |  |
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
> | Microsoft.Web/sites/functions/listSecrets/action | A függvények titkainak listázása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic app-operátor

Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és nem frissíti őket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/*/read | Az információk beolvasása riasztási szabályok |
> | Microsoft. bepillantások/metricAlerts/*/READ |  |
> | Microsoft.Insights/diagnosticSettings/*/read | A Logic Apps diagnosztikai beállításainak beolvasása |
> | Microsoft.Insights/metricDefinitions/*/read | A Logic Apps elérhető metrikáinak beolvasása. |
> | Microsoft.Logic/*/read | Logic Apps erőforrások beolvasása. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | A munkafolyamat engedélyezése. |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Dolgok internetes hálózata


### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription közreműködője

Lehetővé teszi a EventGrid esemény-előfizetési műveletek kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-olvasó

Lehetővé teszi a EventGrid esemény-előfizetések olvasását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription beolvasása |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása topictype szerint |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Felügyelet és irányítás


### <a name="application-insights-component-contributor"></a>Application Insights összetevő közreműködője

Felügyelheti Application Insights összetevőket

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Klasszikus riasztási szabályok létrehozása és kezelése |
> | Microsoft. bepillantások/metricAlerts/* | Új riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft.Insights/webtests/* | Webes tesztek létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger

Felhasználói jogosultságot biztosít a Application Insights Snapshot Debugger gyűjtött hibakeresési Pillanatképek megtekintéséhez és letöltéséhez. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [tulajdonos](#owner) vagy a [közreműködő](#contributor) szerepkörben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Automation-feladatok operátora

Feladatok létrehozása és kezelése Automation-Runbookok használatával.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operátor

Az Automation-operátorok képesek a feladatok elindítására, leállítására, felfüggesztésére és folytatására

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook operátor

A Runbook tulajdonságainak olvasása – a Runbook feladatai létrehozásához.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation runbook beolvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Számlázás olvasója

Olvasási hozzáférés engedélyezése a számlázási információkhoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Billing/*/read | Számlázási adatok olvasása |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Tervezet közreműködője

Kezelheti a terv definícióit, de nem rendelheti hozzá őket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Blueprint/tervezetek/* | Terv-definíciók vagy tervrajz-összetevők létrehozása és kezelése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Blueprint operátor

Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új tervrajzokat. 

> [!NOTE] 
> Ez csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Blueprint/blueprintAssignments/* | Terv-hozzárendelések létrehozása és kezelése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Cost Management-közreműködő

Megtekintheti a költségeket és kezelheti a költségek konfigurációját (pl. költségvetés, exportálás)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. fogyasztás/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft. számlázás/billingPeriods/olvasás |  |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Cost Management olvasó

Megtekintheti a költségeket és a konfigurációt (például a költségvetést, az exportot)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft. számlázás/billingPeriods/olvasás |  |
> | Microsoft. Resources/előfizetések/olvasás | Az előfizetések listájának beolvasása. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Felügyelt alkalmazás közreműködői szerepköre

Lehetővé teszi a felügyelt alkalmazások erőforrásainak létrehozását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Solutions/alkalmazások/* |  |
> | Microsoft.Solutions/register/action | Regisztráljon a megoldásokra. |
> | Microsoft. Resources/Subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Felügyelt alkalmazás operátori szerepköre

Lehetővé teszi műveletek olvasását és végrehajtását a felügyelt alkalmazás erőforrásain

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Microsoft.Solutions/*/action |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Felügyelt alkalmazások olvasója

Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, valamint a JIT-hozzáférés kérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Solutions/jitRequests/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre

A felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre lehetővé teszi a bérlői felhasználók számára a bérlőhöz rendelt regisztrációs hozzárendelés törlését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Felügyeleti csoport közreműködője

Felügyeleti csoport közreműködői szerepköre

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Az előfizetés társítása a felügyeleti csoportból. |
> | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Felügyeleti csoport olvasója

Felügyeleti csoport olvasójának szerepköre

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Közreműködő figyelése

Beolvashatja az összes figyelési és a figyelési beállításokat. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft. bepillantások/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az adatáttekintési összetevők létrehozása és kezelése |
> | Microsoft.Insights/DiagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft.Insights/eventtypes/* | Az előfizetésben szereplő tevékenység-naplózási események (kezelési események) listázása. Ez az engedély mind a programozási, mind a portálhoz való hozzáférésre alkalmazható a tevékenység naplójában. |
> | Microsoft.Insights/LogDefinitions/* | Ez az engedély olyan felhasználók számára szükséges, akiknek a portálon keresztül kell hozzáférnie a tevékenység naplóihoz. A műveletnapló kategóriáinak listázása a tevékenység naplójában. |
> | Microsoft. bepillantások/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | A metrikai definíciók (az adott erőforráshoz elérhető metrikai típusok listája) olvasása. |
> | Microsoft.Insights/Metrics/* | Erőforrás metrikáinak olvasása. |
> | Microsoft.Insights/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft. bepillantások/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Webes tesztek létrehozása és kezelése |
> | Microsoft. bepillantások/munkafüzetek/* |  |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Figyelési metrikák közzétevője

Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Insights/Register/Action | A Microsoft bepillantást nyújtó szolgáltató regisztrálása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Mérőszámok írása |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Figyelő olvasó

Az összes figyelési adat (mérőszámok, naplók stb.) olvasása. Lásd még: Ismerkedés [a szerepkörökkel, az engedélyekkel és a biztonsággal a Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Új ereklye APM-fiók közreműködői

Lehetővé teszi New Relic Application Performance Management-fiókok és-alkalmazások kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | NewRelic.APM/accounts/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Házirend-elemzések adatírója (előzetes verzió)

Olvasási hozzáférést biztosít az erőforrás-házirendekhez, és írási hozzáférést biztosít az erőforrás-összetevőkre vonatkozó házirend eseményeihez.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Erőforrás-szabályzat közreműködője

Az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és az erőforrások/hierarchia olvasásához jogosultsággal rendelkező felhasználók.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */read | Az összes típusú erőforrás beolvasása, kivéve a titkokat. |
> | Microsoft. Authorization/policyassignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policydefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policysetdefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Ütemező – feladattípusok közreműködői

Lehetővé teszi a Scheduler-feladatok összegyűjtését, de nem fér hozzájuk.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Scheduler/jobcollections/* | Feladatok gyűjteményének létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Site Recovery közreműködő

Lehetővé teszi Site Recovery szolgáltatás felügyeletét, kivéve a tár létrehozását és a szerepkör-hozzárendelést

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Recovery Services-tárolóra vonatkozó olvasási riasztások |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/replicationOperationStatus/READ | A tár replikációs művelet állapotának beolvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Site Recovery operátor

Lehetővé teszi a feladatátvételt és a feladat-visszavételt, de nem hajt végre más Site Recovery felügyeleti műveleteket

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Riasztási beállítások beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | A háló konzisztenciájának ellenőrzése |
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
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Védelmi tároló váltása |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Site Recovery olvasó

Lehetővé teszi, hogy megtekintse Site Recovery állapotát, de ne végezzen más felügyeleti műveleteket

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
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
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Támogatási kérelem közreműködői

Lehetővé teszi a támogatási kérések létrehozását és kezelését

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Munkafüzet közreműködője

Menthetők a megosztott munkafüzetek.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. bepillantások/munkafüzetek/írás | Munkafüzet létrehozása vagy frissítése |
> | Microsoft. bepillantások/munkafüzetek/törlés | Munkafüzet törlése |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Munkafüzet-olvasó

A munkafüzetek beolvasása.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Vegyes valóság


### <a name="spatial-anchors-account-contributor"></a>Térbeli horgonyok fiók közreműködője

Lehetővé teszi a térbeli horgonyok kezelését a fiókban, de nem törli őket

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiókjának tulajdonosa

Lehetővé teszi a fiókban lévő térbeli horgonyok kezelését, beleértve azok törlését is

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiókjának olvasója

Lehetővé teszi a fiókban található térbeli horgonyok tulajdonságainak megkeresését és beolvasását

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Hálózat


### <a name="cdn-endpoint-contributor"></a>CDN-végpont közreműködői

Kezelheti a CDN-végpontokat, de nem tud hozzáférést biztosítani más felhasználóknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-végpont olvasója

Megtekintheti a CDN-végpontokat, de nem végezheti el a módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>CDN-profil közreműködői

Kezelheti a CDN-profilokat és azok végpontjait, de nem adhat hozzáférést más felhasználóknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can’t grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-profil olvasója

Megtekintheti a CDN-profilokat és azok végpontjait, de nem végezhet módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can’t make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Klasszikus hálózati közreműködő

Lehetővé teszi a klasszikus hálózatok kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicNetwork/* | Klasszikus hálózatok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DNS-zóna közreműködője

Lehetővé teszi a DNS-zónák és-rekordhalmazok kezelését Azure DNSban, de nem teszi lehetővé, hogy ki férhet hozzájuk.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és-rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Hálózati közreműködő

Lehetővé teszi a hálózatok kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/* | Hálózatok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager közreműködő

Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé az azokhoz való hozzáférés szabályozását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Biztonság


### <a name="azure-sentinel-contributor"></a>Azure Sentinel közreműködő

Azure Sentinel közreműködő

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/read | Meglévő munkaterület beolvasása |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/* |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel-olvasó

Azure Sentinel-olvasó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/*/READ |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/read | Meglévő munkaterület beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Microsoft.OperationsManagement/solutions/read | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel-válaszadó

Azure Sentinel-válaszadó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. SecurityInsights/*/READ |  |
> | Microsoft. SecurityInsights/esetek/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Microsoft.OperationalInsights/workspaces/read | Meglévő munkaterület beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Microsoft.OperationsManagement/solutions/read | OMS-megoldás beolvasása |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Microsoft. OperationalInsights/munkaterületek/lekérdezés/*/READ |  |
> | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Microsoft. bepillantások/munkafüzetek/olvasás | Munkafüzet beolvasása |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Key Vault közreműködő

Lehetővé teszi a kulcstartók kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Biztonsági rendszergazda

Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és a javaslatokat, valamint

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft. Authorization/policyAssignments/* | Szabályzat-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policyDefinitions/* | Szabályzat-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policySetDefinitions/* | Szabályzat-készletek létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatértékek megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Biztonsági értékelő közreműködő

Lehetővé teszi az értékelések leküldését Security Center

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Security/értékelések/írás | Biztonsági értékelések létrehozása vagy frissítése az előfizetésen |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Security Manager (örökölt)

Ez egy örökölt szerepkör. Használja helyette a biztonsági rendszergazdát.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicCompute/*/read | Konfigurációs információk beolvasása a klasszikus virtuális gépeken |
> | Microsoft.ClassicCompute/virtualMachines/*/write | A klasszikus virtuális gépek írási konfigurációja |
> | Microsoft.ClassicNetwork/*/read | A klasszikus hálózat konfigurációs információinak olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Biztonsági olvasó

Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem végezhet módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.operationalInsights/workspaces/*/read | Log Analytics-adatértékek megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Security/*/read | Biztonsági összetevők és szabályzatok olvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Tárterület


### <a name="avere-contributor"></a>Avere közreműködője

Létrehozhat és kezelhet egy avere vFXT-fürtöt.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/READ | Az erőforráscsoport erőforrásainak beolvasása. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere operátor

A avere vFXT-fürt által használt fürt kezelése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy Blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Egy blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Biztonsági mentési közreműködő

Lehetővé teszi a Backup szolgáltatás kezelését, de nem hozhat létre tárolókat, és hozzáférést biztosíthat másoknak

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A művelet eredményeinek kezelése a biztonsági mentési felügyeletben |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Biztonsági mentési tárolók létrehozása és kezelése Recovery Services-tár biztonsági mentési hálójában |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
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
> | Microsoft.RecoveryServices/Vaults/write | A Create Vault művelet létrehoz egy "Vault" típusú Azure-erőforrást. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Biztonságimásolat-felelős

Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tár létrehozását és a másokhoz való hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | A védett elemek biztonsági mentését végzi. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | A védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Védett elemek biztonsági másolatának létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | A biztonságimásolat-kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A házirend-művelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Olyan elemek létrehozása és kezelése, amelyekről biztonsági másolatot lehet készíteni |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | A művelet érvényesítése védett elemen |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend-művelet állapotának beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Létrehoz egy regisztrált tárolót |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | A tárolóban lévő számítási feladatok lekérdezése |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Backup-olvasó

Megtekintheti a biztonsági mentési szolgáltatásokat, de nem végezheti el a módosításokat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A lefoglalt bélyegző beolvasása a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | A védelmi tárolón végrehajtott művelet eredményét kapja meg. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | A védett elemeken végrehajtott művelet eredményét kapja meg. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | A védett elemek helyreállítási pontjainak beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatok műveletének eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Az összes feladatütemezés visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Recovery Services-tár biztonsági mentési műveletének eredményét adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A házirend-művelet eredményeinek beolvasása. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi szabályzat visszaadása |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A kiterjesztett adatok beolvasása művelet lekérdezi egy objektum kiterjesztett adatait, amely a (z) típusú Azure-erőforrást jelképezi? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Recovery Services-tároló konfigurációjának visszaadása. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Házirend-művelet állapotának beolvasása. |
> | Microsoft. Recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tár összes regisztrált biztonságimásolat-felügyeleti kiszolgálóját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági másolat védelmi szándékának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Tároló összes elemének beolvasása |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Microsoft.RecoveryServices/Vaults/usages/read | Egy Recovery Services tár használati adatait adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Klasszikus Storage-fiók közreműködői

Lehetővé teszi a klasszikus Storage-fiókok kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicStorage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>A klasszikus Storage-fiók kulcs-kezelő szolgáltatásának szerepköre

A klasszikus Storage-fiók kulcsfontosságú operátorai jogosultak a kulcsok listázására és újragenerálása a klasszikus Storage-fiókokban

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Felsorolja a Storage-fiókok hozzáférési kulcsait. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Újragenerálja a Storage-fiók meglévő hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box közreműködő

A lehetővé teszi, hogy minden Data Box szolgáltatás alatt kezelje a hozzáférést, kivéve, ha mások számára biztosít hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Databox/* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Data Box olvasó

Lehetővé teszi Data Box szolgáltatás felügyeletét, kivéve a rendelés vagy a szerkesztési sorrend részleteit, és hozzáférést biztosít másoknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Felsorolja a rendeléshez kapcsolódó titkosítatlan hitelesítő adatokat. |
> | Microsoft.Databox/locations/availableSkus/action | Ez a metódus visszaadja az elérhető SKU-ket tartalmazó listát. |
> | Microsoft. Databox/Locations/validateInputs/Action | Ez a metódus az összes érvényesítési típust támogatja. |
> | Microsoft. Databox/Locations/regionConfiguration/Action | Ez a metódus a régió konfigurációit adja vissza. |
> | Microsoft.Databox/locations/validateAddress/action | Ellenőrzi a szállítási címet, és alternatív címeket biztosít, ha vannak ilyenek. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Data Lake Analytics fejlesztő

Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de Data Lake Analytics fiókokat nem hozhat létre vagy törölhet.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
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
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Olvasó és adathozzáférés

Lehetővé teszi az összes megtekintését, de nem engedélyezheti a Storage-fiók vagy a tárolt erőforrás törlését vagy létrehozását. Az olvasási/írási hozzáférést is lehetővé teszi a Storage-fiókban található összes, a Storage-fiók kulcsaihoz való hozzáférés útján.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | A megadott Storage-fiókhoz tartozó SAS-tokent adja vissza. |
> | Microsoft.Storage/storageAccounts/read | A Storage-fiókok listáját adja vissza, vagy lekéri a megadott Storage-fiók tulajdonságait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Storage-fiók közreműködői

Engedélyezi a Storage-fiókok kezelését. Hozzáférést biztosít a fiók kulcsaként, amely a megosztott kulcsos hitelesítésen keresztüli hozzáféréshez használható.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Létrehozza, frissíti vagy beolvassa a Analysis Server diagnosztikai beállításait. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>A Storage-fiók kulcs-kezelő szolgáltatásának szerepköre

Engedélyezi a Storage-fiók hozzáférési kulcsainak listázását és újragenerálását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott Storage-fiókhoz tartozó hozzáférési kulcsokat adja vissza. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Újragenerálja a megadott Storage-fiók hozzáférési kulcsait. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage blob adatközreműködői

Azure Storage-tárolók és-Blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tároló vagy tárolók listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Egy tároló metaadatainak vagy tulajdonságainak módosítása. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | BLOB törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB vagy Blobok listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Írás blobba. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Storage blob-adattulajdonos

Teljes hozzáférést biztosít az Azure Storage blob-tárolók és-adatkészletekhez, beleértve a POSIX hozzáférés-vezérlés hozzárendelését. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | A tárolók teljes engedélyei. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | A Blobok teljes engedélyei. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Storage blob-Adatolvasó

Azure Storage-tárolók és-Blobok olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tároló vagy tárolók listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | BLOB vagy Blobok listájának visszaadása. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Storage blob-delegáló

Felhasználói delegálási kulcs beszerzése, amely az Azure AD-beli hitelesítő adatokkal aláírt tárolók vagy Blobok közös hozzáférési aláírásának létrehozásához használható. További információt a [felhasználói delegálási sas létrehozása](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas)című témakörben talál.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | A Blob service felhasználói delegálási kulcsát adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Storage file-adatsmb-megosztás közreműködői

Lehetővé teszi az olvasási, írási és törlési hozzáférést az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ehhez a szerepkörhöz nem tartozik a Windows-fájlkiszolgálók beépített megfelelője.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage file-adatsmb-megosztás emelt szintű közreműködője

Lehetővé teszi az olvasási, írási, törlési és módosítási ACL-eket az Azure-fájlmegosztás fájljain vagy könyvtárain. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók változásának fájlmegosztás ACL-jéhez.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Egy fájl írásának vagy egy mappa létrehozásának eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Egy fájl vagy mappa törlésének eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Egy fájlra vagy mappára vonatkozó engedély módosításának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Storage-fájl adatsmb-megosztásának olvasója

Olvasási hozzáférés engedélyezése az Azure-fájlmegosztás fájljaihoz vagy könyvtáraihoz. Ez a szerepkör egyenértékű a Windows-fájlkiszolgálók olvasásához szükséges fájlmegosztási ACL-vel.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Egy fájlt/mappát vagy egy fájl/mappa listáját adja vissza. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Tárolási várólista adatközreműködői

Azure Storage-várólisták és üzenetsor-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Várólista törlése. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólista vagy várólisták listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Várólista metaadatainak vagy tulajdonságainak módosítása |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Egy vagy több üzenet törlése egy várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Tárolási üzenetsor adatüzenet-processzora

Üzenet betekintése, beolvasása és törlése egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Üzenet bepillantása. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet beolvasása és törlése. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Tárolási várólista adatüzenetének küldője

Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadása egy várólistához. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Storage-várólista adatolvasója

Azure Storage-várólisták és üzenetsor-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy mely műveletek szükségesek egy adott adatművelethez, tekintse meg a [blob-és üzenetsor-műveletek meghívására vonatkozó engedélyeket](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólistát vagy várólisták listáját adja vissza. |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy vagy több üzenet betekintése vagy lekérése egy várólistából. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-reader-preview"></a>Azure Maps Adatolvasó (előzetes verzió)

Hozzáférést biztosít egy Azure Maps-fiókból az olvasási leképezéssel kapcsolatos adatokhoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nEz egy* |  |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Search Service közreműködő

Lehetővé teszi a keresési szolgáltatások kezelését, az azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Search/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Webes csomag közreműködői

Lehetővé teszi a webhelyek webes csomagjainak kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/serverFarms/* | Kiszolgálófarm létrehozása és kezelése |
> | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Webhely közreműködői

Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de nem fér hozzájuk.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
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
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Egyéb


### <a name="biztalk-contributor"></a>BizTalk közreműködő

Lehetővé teszi a BizTalk Services kezelését, de azokhoz való hozzáférés nélkül.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft. Authorization/*/READ | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk Services-szolgáltatások létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az adatáttekintési riasztási szabályok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | A megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotának beolvasása |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok beolvasása vagy felsorolása. |
> | Microsoft.Support/* | Támogatási jegyek létrehozása és kezelése |
> | **Nincs tapintat** |  |
> | *nEz egy* |  |
> | **DataActions** |  |
> | *nEz egy* |  |
> | **NotDataActions** |  |
> | *nEz egy* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
