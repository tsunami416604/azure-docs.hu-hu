---
title: Azure Beépített szerepkörök az Azure RBAC-hoz
description: Ez a cikk ismerteti az Azure beépített szerepkörök az Azure szerepköralapú hozzáférés-vezérlés (RBAC). A Műveletek, NotActions, DataActions és NotDataActions műveleteket sorolja fel.
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
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: ba07a0b0d3be0366179bba14c786fafad8753dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280694"
---
# <a name="azure-built-in-roles"></a>Az Azure beépített szerepkörei

[Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) számos Beépített Azure-szerepkörrel rendelkezik, amelyeket felhasználókhoz, csoportokhoz, egyszerű szolgáltatáscsoportokhoz és felügyelt identitásokhoz rendelhet hozzá. A szerepkör-hozzárendelések segítségével szabályozhatja az Azure-erőforrásokhoz való hozzáférést. Ha a beépített szerepkörök nem felelnek meg a szervezet egyedi igényeinek, létrehozhat saját [Egyéni Azure-szerepköröket.](custom-roles.md)

Ez a cikk az Azure-erőforrások beépített szerepköreit sorolja fel, amelyek folyamatosan fejlődnek. A legújabb szerepkörök beszerezéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy [az az szerepkördefiníciós listát.](/cli/azure/role/definition#az-role-definition-list) Ha rendszergazdai szerepköröket keres az Azure Active Directoryhoz (Azure AD), olvassa el [a Rendszergazdai szerepkör-engedélyek az Azure Active Directoryban című témakört.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="all"></a>Összes

Az alábbi táblázat rövid leírást és az egyes beépített szerepkörek egyedi azonosítóját tartalmazza. Válassza ki a szerepkör nevét `Actions` `NotActions`a `DataActions`, `NotDataActions` , , és az egyes szerepkörek listájának megtekintéséhez. Ha tudni szeretné, hogy mit jelentenek ezek a műveletek, és hogyan vonatkoznak a felügyeleti és adatsíkokra, [olvassa el az Azure-erőforrások szerepkör-definícióinak ismertetése című témakört.](role-definitions.md)


> [!div class="mx-tableFixed"]
> | Beépített szerepkör | Leírás | ID (Azonosító) |
> | --- | --- | --- |
> | **Általános** |  |  |
> | [Közreműködő](#contributor) | Lehetővé teszi, hogy az erőforrásokhoz való hozzáférést kivéve mindent kezeljen. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Tulajdonos](#owner) | Lehetővé teszi, hogy mindent kezeljen, beleértve az erőforrásokhoz való hozzáférést is. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Olvasó](#reader) | Lehetővé teszi, hogy mindent megtekint, de nem hajt végre módosításokat. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Felhasználói hozzáférés adminisztrátora](#user-access-administrator) | Lehetővé teszi az Azure-erőforrásokhoz való felhasználói hozzáférés kezelését. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Klasszikus virtuális gép közreműködője](#classic-virtual-machine-contributor) | Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem férhet hozzájuk, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Virtuálisgép-rendszergazda bejelentkezés](#virtual-machine-administrator-login) | Virtuális gépek megtekintése a portálon, és bejelentkezés rendszergazdaként | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuális gépek közreműködője](#virtual-machine-contributor) | Lehetővé teszi a virtuális gépek kezelését, de a hozzájuk való hozzáférést, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Virtuálisgép felhasználói bejelentkezése](#virtual-machine-user-login) | Tekintse meg a virtuális gépek a portálon, és jelentkezzen be, mint egy rendszeres felhasználó. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Hálózat** |  |  |
> | [CDN végpont közreműködője](#cdn-endpoint-contributor) | CdN-végpontok kezelésére, de nem adhat hozzáférést más felhasználóknak. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN végpontolvasó](#cdn-endpoint-reader) | A CDN-végpontok megtekintése, de nem végezhet módosításokat. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil közreműködője](#cdn-profile-contributor) | A CDN-profilok és azok végpontjai kezelhetők, de nem adhatnak hozzáférést más felhasználóknak. | ec156ff8-a8d1-4d15-830c-5b80698c432 |
> | [CDN-profilolvasó](#cdn-profile-reader) | Megtekintheti a CDN-profilokat és azok végpontjait, de nem módosíthatja őket. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klasszikus hálózati közreműködő](#classic-network-contributor) | Lehetővé teszi a klasszikus hálózatok kezelését, de a hozzájuk való hozzáférést nem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DNS-zóna közreműködője](#dns-zone-contributor) | Lehetővé teszi a DNS-zónák és rekordkészletek kezelését az Azure DNS-ben, de nem teszi lehetővé, hogy szabályozhassa, ki férhet hozzá. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Hálózati közreműködő](#network-contributor) | Lehetővé teszi a hálózatok kezelését, de a hozzáférésüket nem. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Traffic Manager közreműködő](#traffic-manager-contributor) | Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Tárterület** |  |  |
> | [Avere közreműködő](#avere-contributor) | Avere vFXT-fürt ötös fürtöt hozhat létre és kezelhet. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere operátor](#avere-operator) | Az Avere vFXT-fürt használja a fürt kezeléséhez | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Biztonsági másolat közreműködője](#backup-contributor) | Lehetővé teszi a biztonsági mentési szolgáltatás kezelését, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Biztonsági másolat operátora](#backup-operator) | Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tároló létrehozását és a másokhoz való hozzáférést | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Biztonsági másolat olvasója](#backup-reader) | Megtekintheti a biztonsági mentési szolgáltatásokat, de nem módosíthatja | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Klasszikus tárfiók közreműködője](#classic-storage-account-contributor) | Lehetővé teszi a klasszikus tárfiókok kezelését, de a hozzájuk való hozzáférést nem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klasszikus tárfiókkulcs-operátori szolgáltatás szerepkör](#classic-storage-account-key-operator-service-role) | A klasszikus tárfiókkulcs-operátorok felsorolhatják és újragenerálhatják a kulcsokat a klasszikus tárfiókokban | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Adatmező közreműködője](#data-box-contributor) | Lehetővé teszi, hogy mindent kezeljen a Data Box Szolgáltatás alatt, kivéve mások hozzáférését. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Adatdoboz-olvasó](#data-box-reader) | Lehetővé teszi a Data Box Szolgáltatás kezelését, kivéve a sorrend létrehozását vagy a rendelés részleteinek szerkesztését, valamint a másokhoz való hozzáférést. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [A Data Lake Analytics fejlesztője](#data-lake-analytics-developer) | Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de a Data Lake Analytics-fiókok létrehozását és törlését nem. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Olvasó- és adathozzáférés](#reader-and-data-access) | Lehetővé teszi, hogy mindent megtekint, de nem teszi lehetővé, hogy törölje, vagy hozzon létre egy tárfiókot vagy zárt erőforrást. Emellett lehetővé teszi az olvasási/írási hozzáférést a tárfiókban található összes adathoz a tárfiók kulcsaihoz való hozzáférés révén. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Tárfiók-közreműködő](#storage-account-contributor) | Lehetővé teszi a tárfiókok kezelését. Hozzáférést biztosít a fiókkulcshoz, amellyel a megosztott kulcs hitelesítésével lehet hozzáférni az adatokhoz. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Tárfiókkulcs-operátor szolgáltatásszerepköre](#storage-account-key-operator-service-role) | Lehetővé teszi a tárfiók hozzáférési kulcsainak listázását és újragenerálását. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob-adatközreműködő](#storage-blob-data-contributor) | Azure Storage-tárolók és -blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Tárolási blob-adatok tulajdonosa](#storage-blob-data-owner) | Teljes hozzáférést biztosít az Azure Storage blobtárolóihoz és adataihoz, beleértve a POSIX hozzáférés-vezérlés hozzárendelését is. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Tárolási blob adatolvasó](#storage-blob-data-reader) | Az Azure Storage-tárolók és -blobok olvasása és listázása. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Tárolási blob delegator](#storage-blob-delegator) | Szerezzen be egy felhasználói delegálási kulcsot, amely ezután egy Azure AD-hitelesítő adatokkal aláírt tároló vagy blob megosztott hozzáférési aláírásának létrehozásához használható. További információ: [Felhasználói delegáláslétrehozása SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Tárolófájl-adat SMB-megosztás közreműködője](#storage-file-data-smb-share-contributor) | Lehetővé teszi az Azure-fájlmegosztásokban lévő fájlok/könyvtárak olvasását, írását és törlését. Ez a szerepkör nem rendelkezik beépített megfelelővel a Windows fájlkiszolgálókon. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Storage File Data SMB share emelt szintű közreműködő](#storage-file-data-smb-share-elevated-contributor) | Lehetővé teszi az Azure-fájlmegosztások fájljainak és könyvtáraihoz tartozó ACL-ek olvasását, írását, törlését és módosítását. Ez a szerepkör egyenértékű a Windows fájlkiszolgálókon a módosítás fájlmegosztási acl-jével. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Tárolófájl-adat SMB-megosztásolvasó](#storage-file-data-smb-share-reader) | Lehetővé teszi az olvasási hozzáférést a fájlokhoz/könyvtárakhoz az Azure-fájlmegosztásokban. Ez a szerepkör egyenértékű a Windows fájlkiszolgálókon olvasott fájlmegosztási acl-vel. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Tárolási várólista adatközreműködője](#storage-queue-data-contributor) | Az Azure Storage-várólisták és várólista-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Tárolóvárólista adatüzenet-feldolgozója](#storage-queue-data-message-processor) | Betekintés, beolvasása és törlése egy üzenetet egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Tárolási várólista-adatüzenet-küldő](#storage-queue-data-message-sender) | Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Tárolóvárólista adatolvasója](#storage-queue-data-reader) | Az Azure Storage-várólisták és várólista-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Web** |  |  |
> | [Azure Maps adatolvasó (előzetes verzió)](#azure-maps-data-reader-preview) | Hozzáférést biztosít az Azure maps-fióktérképekkel kapcsolatos adatok olvasásához. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Keresési szolgáltatás közreműködője](#search-service-contributor) | Lehetővé teszi a Keresési szolgáltatások kezelését, de a hozzáférésüket nem. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Webes terv közreműködője](#web-plan-contributor) | Lehetővé teszi a webhelyek webes csomagjainak kezelését, de a hozzáférésüket nem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webhely közreműködője](#website-contributor) | Lehetővé teszi a webhelyek kezelését (nem webes sémákat), de nem férhet hozzájuk. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Tárolók** |  |  |
> | [AcrDelete](#acrdelete) | acr törlés | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr kép aláírója | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull között](#acrpull) | acr húzás | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush között](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | acr karantén adatolvasó | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr karantén adatíró | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes szolgáltatásfürt felügyeleti szerepkör](#azure-kubernetes-service-cluster-admin-role) | Fürtfelügyeleti hitelesítő adatok műveletének listázása. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Azure Kubernetes szolgáltatásfürt felhasználói szerepköre](#azure-kubernetes-service-cluster-user-role) | Fürtfelhasználói hitelesítő adatokkal kapcsolatos műveletének listázása. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Adatbázisok** |  |  |
> | [Cosmos DB-fiókolvasó szerepkör](#cosmos-db-account-reader-role) | Az Azure Cosmos DB-fiók adatai olvasása. Az Azure Cosmos DB-fiókok kezeléséhez tekintse meg a [DocumentDB-fiók közreműködőjét.](#documentdb-account-contributor) | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operátor](#cosmos-db-operator) | Lehetővé teszi az Azure Cosmos DB-fiókok kezelését, de a bennük lévő adatok elérését nem. Megakadályozza a fiókkulcsok hoz és a kapcsolati karakterláncokhoz való hozzáférést. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Cosmos DB-adatbázis vagy egy fiók tárolójának visszaállítási kérelmet küldhet | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [DocumentDB-fiók közreműködője](#documentdb-account-contributor) | Az Azure Cosmos DB-fiókok kezelése. Az Azure Cosmos DB korábbi nevén DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis gyorsítótár közreműködője](#redis-cache-contributor) | Lehetővé teszi a Redis-gyorsítótárak kezelését, de a hozzájuk való hozzáférést nem. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB közreműködő](#sql-db-contributor) | Lehetővé teszi az SQL-adatbázisok kezelését, de a hozzáférésüket nem. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL felügyelt példány közreműködője](#sql-managed-instance-contributor) | Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de nem adhat hozzáférést másoknak. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL biztonsági kezelő](#sql-security-manager) | Lehetővé teszi az SQL-kiszolgálók és adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, de a hozzáférésüket nem. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server közreműködő](#sql-server-contributor) | Lehetővé teszi az SQL-kiszolgálók és adatbázisok kezelését, de a hozzáférésüket nem, és nem a biztonsággal kapcsolatos házirendeket. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Elemzés** |  |  |
> | [Az Azure Event Hubs adatainak tulajdonosa](#azure-event-hubs-data-owner) | Lehetővé teszi az Azure Event Hubs-erőforrások teljes körű elérését. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs adatfogadó](#azure-event-hubs-data-receiver) | Lehetővé teszi az Azure Event Hubs-erőforrások fogadását. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Az Azure Event Hubs adatküldő](#azure-event-hubs-data-sender) | Lehetővé teszi a hozzáférést az Azure Event Hubs erőforrásaihoz. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Adatfeldolgozó közreműködő](#data-factory-contributor) | Adatgyárakat, valamint gyermekerőforrásokat hozhat létre és kezelhet. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Adatpurger](#data-purger) | Az elemzési adatok törlése | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [HDInsight fürtoperátor](#hdinsight-cluster-operator) | Lehetővé teszi a HDInsight-fürtkonfigurációk olvasását és módosítását. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [A HDInsight tartományi szolgáltatások közreműködője](#hdinsight-domain-services-contributor) | A HDInsight vállalati biztonsági csomaghoz szükséges tartományszolgáltatásokolvasás, -létrehozás, -módosítás és -törlés | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics közreműködő](#log-analytics-contributor) | A Log Analytics közreműködője letudja olvasni az összes figyelési adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztése magában foglalja a virtuális gépbővítmény virtuális gépekhez való hozzáadását; a tárfiók kulcsainak olvasása az Azure Storage-ból származó naplók gyűjteményének konfigurálásához; Automatizálási fiókok létrehozása és konfigurálása; megoldások hozzáadása; és konfigurálja az Azure-diagnosztikát az összes Azure-erőforráson. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics olvasó](#log-analytics-reader) | A Log Analytics Reader megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blokklánc** |  |  |
> | [Blokklánc-tagcsomópont-hozzáférés (előzetes verzió)](#blockchain-member-node-access-preview) | Lehetővé teszi a hozzáférést a Blockchain tagcsomópontjaihoz | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + gépi tanulás** |  |  |
> | [Kognitív szolgáltatások közreműködője](#cognitive-services-contributor) | Lehetővé teszi a Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services adatolvasó (előzetes verzió)](#cognitive-services-data-reader-preview) | Lehetővé teszi a Cognitive Services-adatok olvasását. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Kognitív szolgáltatások felhasználója](#cognitive-services-user) | Lehetővé teszi a Cognitive Services kulcsainak olvasását és listázását. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Vegyes valóság** |  |  |
> | [Területi horgonyok fiók közreműködője](#spatial-anchors-account-contributor) | Lehetővé teszi a fiók térbeli horgonyok kezelését, de törlését nem | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Térbeli horgonyok fiók tulajdonosa](#spatial-anchors-account-owner) | Lehetővé teszi a fiók térbeli horgonyok kezelését, beleértve azok törlését is | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Térbeli horgonyok fiókolvasó](#spatial-anchors-account-reader) | Lehetővé teszi a térbeli horgonyok tulajdonságainak megkeresését és olvasását a fiókban | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integráció** |  |  |
> | [API-felügyeleti szolgáltatás közreműködője](#api-management-service-contributor) | Kezelheti a szolgáltatást és az API-kat | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [API-felügyeleti szolgáltatás operátori szerepköre](#api-management-service-operator-role) | Kezelheti a szolgáltatást, de az API-kat nem | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [API-kezelés szolgáltatásolvasó szerepkör](#api-management-service-reader-role) | Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Alkalmazáskonfigurációs adatok tulajdonosa](#app-configuration-data-owner) | Lehetővé teszi az alkalmazáskonfigurációs adatok teljes körű elérését. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Alkalmazáskonfigurációs adatolvasó](#app-configuration-data-reader) | Lehetővé teszi az olvasási hozzáférést az alkalmazáskonfigurációs adatokhoz. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Az Azure Service Bus adatainak tulajdonosa](#azure-service-bus-data-owner) | Lehetővé teszi az Azure Service Bus erőforrásainak teljes körű elérését. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus adatfogadó](#azure-service-bus-data-receiver) | Lehetővé teszi az Azure Service Bus-erőforrások fogadását. | 4f6d3b9b-027b-4f4c-9142-0e5a2a22247e0 |
> | [Az Azure Service Bus adatszolgáltatója](#azure-service-bus-data-sender) | Lehetővé teszi az Azure Service Bus-erőforrások hoz való hozzáférés küldését. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Az Azure Stack regisztrációs tulajdonosa](#azure-stack-registration-owner) | Lehetővé teszi az Azure Stack-regisztrációk kezelését. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [EventGrid EventSubscription közreműködő](#eventgrid-eventsubscription-contributor) | Lehetővé teszi az EventGrid esemény-előfizetési műveletek kezelését. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-olvasó](#eventgrid-eventsubscription-reader) | Lehetővé teszi az EventGrid esemény-előfizetések olvasását. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Intelligens rendszerek fiók közreműködője](#intelligent-systems-account-contributor) | Lehetővé teszi az Intelligens rendszerek fiókjainak kezelését, de a hozzáférésüket nem. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Logikai alkalmazás közreműködője](#logic-app-contributor) | Lehetővé teszi a logikai alkalmazások kezelését, de a hozzáférés módosítását nem. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logikai alkalmazás operátora](#logic-app-operator) | Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és frissítheti azokat. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitás** |  |  |
> | [Felügyelt identitás közreműködője](#managed-identity-contributor) | Felhasználó hozzárendelt identitásának létrehozása, olvasása, frissítése és törlése | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Felügyelt identitásoperátor](#managed-identity-operator) | Felhasználó hozrendelt identitásának olvasása és hozzárendelése | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Biztonság** |  |  |
> | [Az Azure Sentinel közreműködője](#azure-sentinel-contributor) | Az Azure Sentinel közreműködője | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel olvasó](#azure-sentinel-reader) | Azure Sentinel olvasó | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel válaszadó](#azure-sentinel-responder) | Azure Sentinel válaszadó | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Kulcstartó közreműködője](#key-vault-contributor) | Lehetővé teszi a kulcstartók kezelését, de a hozzáférésüket nem. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Biztonsági rendszergazda](#security-admin) | Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és javaslatokat, elvetheti a riasztásokat és a javaslatokat. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Biztonsági értékelés közreműködője](#security-assessment-contributor) | Lehetővé teszi az értékelések leküldése a Security Centerbe | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Biztonsági kezelő (örökölt)](#security-manager-legacy) | Ez egy örökölt szerep. Használja inkább a Biztonsági rendszergazda használatát. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Biztonsági olvasó](#security-reader) | Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem módosíthatja azokat. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs felhasználó](#devtest-labs-user) | Lehetővé teszi a virtuális gépek csatlakoztatását, indítását, újraindítását és leállítását az Azure DevTest Labs ben. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Labor készítője](#lab-creator) | Lehetővé teszi a felügyelt laborok létrehozását, kezelését és törlését az Azure Lab-fiókok alatt. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Figyelő** |  |  |
> | [Az Application Insights összetevőközreműködője](#application-insights-component-contributor) | Az Application Insights-összetevők kezelése | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Az Application Insights pillanatkép-hibakereső](#application-insights-snapshot-debugger) | A felhasználó engedélyt ad az Application Insights Snapshot Debugger által gyűjtött hibakeresési pillanatképek megtekintésére és letöltésére. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [Tulajdonos](#owner) vagy [a Közreműködő](#contributor) szerepkörben. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Közreműködő figyelése](#monitoring-contributor) | Képes olvasni az összes figyelési adatokat, és szerkesztheti monitoring beállításokat. Lásd [még: A szerepkörökkel, engedélyekkel és biztonsággal kapcsolatos első lépések az Azure Monitor segítségével.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles) | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Monitormetrika közzétevő](#monitoring-metrics-publisher) | Engedélyezi a metrikák közzétételét az Azure-erőforrásokkal szemben | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Monitoring olvasó](#monitoring-reader) | Képes olvasni az összes figyelési adatok (metrikák, naplók, stb.) Lásd [még: A szerepkörökkel, engedélyekkel és biztonsággal kapcsolatos első lépések az Azure Monitor segítségével.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles) | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Munkafüzet közreműködője](#workbook-contributor) | Mentheti a közös munkafüzeteket. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Munkafüzet-olvasó](#workbook-reader) | Képes olvasni a munkafüzeteket. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Menedzsment + irányítás** |  |  |
> | [Automatizálási feladat operátor](#automation-job-operator) | Feladatok létrehozása és kezelése automatizálási runbookokkal. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operátor](#automation-operator) | Az Automatizálási operátorok elindíthatják, leállíthatják, felfüggeszthetik és folytathatják a feladatokat | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automatizálási runbook-operátor](#automation-runbook-operator) | Olvassa el a Runbook-tulajdonságokat – a runbook-feladatok létrehozásához. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Azure connected machine bevezetés](#azure-connected-machine-onboarding) | Az Azure Connected Machines-t belehet kapcsolni. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure Connected Machine erőforrás-rendszergazda](#azure-connected-machine-resource-administrator) | Olvashat, írhat, törölhet és újra felveheti az Azure Connected Machines gépeket. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Számlázási olvasó](#billing-reader) | Lehetővé teszi az olvasási hozzáférést a számlázási adatokhoz | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Blueprint közreműködő](#blueprint-contributor) | Kezelheti a tervezetdefiníciókat, de nem rendelheti hozzá őket. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Terv operátor](#blueprint-operator) | Meglévő közzétett tervrajzok hozzárendelése, de nem hozhat létre új tervrajzokat. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés egy felhasználó által hozzárendelt felügyelt identitással történik. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Költségkezelés közreműködője](#cost-management-contributor) | Megtekintheti a költségeket és kezelheti a költségkonfigurációt (pl. költségvetések, export) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Költségkezelési olvasó](#cost-management-reader) | Megtekintheti a költségadatokat és a konfigurációt (pl. költségvetések, exportok) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Felügyelt alkalmazásközreműködői szerepkör](#managed-application-contributor-role) | Lehetővé teszi a felügyelt alkalmazás-erőforrások létrehozását. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Felügyelt alkalmazásfelelős szerepkör](#managed-application-operator-role) | Lehetővé teszi a felügyelt alkalmazás-erőforrások olvasását és műveletek elvégzését | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Felügyelt alkalmazások olvasója](#managed-applications-reader) | Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, és jit-hozzáférés kérése. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepköre](#managed-services-registration-assignment-delete-role) | A felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkör lehetővé teszi, hogy a kezelő bérlői felhasználók töröljék a bérlőhöz rendelt regisztrációs hozzárendelést. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Felügyeleti csoport közreműködője](#management-group-contributor) | Felügyeleti csoport közreműködői szerepkör | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Felügyeleti csoport olvasója](#management-group-reader) | Felügyeleti csoport olvasói szerepköre | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Új Relic APM-fiók közreműködője](#new-relic-apm-account-contributor) | Lehetővé teszi az új relic alkalmazásteljesítmény-kezelési fiókok és alkalmazások kezelését, de a hozzájuk való hozzáférést nem. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Házirend-elemzési adatok megírója (előzetes verzió)](#policy-insights-data-writer-preview) | Lehetővé teszi az olvasási hozzáférést az erőforrás-házirendekhez és az írási hozzáférést az erőforrás-összetevők házirendeseményeihez. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Erőforrás-szabályzat közreműködője](#resource-policy-contributor) | Az erőforrás-házirend létrehozására/módosítására, támogatási jegy létrehozására és az erőforrások/hierarchia olvasására jogosult felhasználók. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Webhely-helyreállítási közreműködő](#site-recovery-contributor) | Lehetővé teszi a Site Recovery szolgáltatás kezelését, kivéve a tároló létrehozását és a szerepkör-hozzárendelést | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Hely-helyreállítási operátor](#site-recovery-operator) | Lehetővé teszi a feladatátvételt és a feladatátvételt, de nem hajthatja végre a site recovery felügyeleti műveleteit | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Webhely-helyreállítási olvasó](#site-recovery-reader) | Lehetővé teszi a Webhely-helyreállítási állapot megtekintését, de más felügyeleti műveletek elvégzését nem | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Támogatási kérelem közreműködője](#support-request-contributor) | Lehetővé teszi támogatási kérelmek létrehozását és kezelését | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Címke közreműködő](#tag-contributor) | Lehetővé teszi az entitások címkéinek kezelését anélkül, hogy magukhoz az entitásokhoz biztosítana hozzáférést. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Egyéb** |  |  |
> | [BizTalk közreműködő](#biztalk-contributor) | Lehetővé teszi a BizTalk szolgáltatások kezelését, de a hozzáférésüket nem. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Ütemező feladatgyűjtemények közreműködője](#scheduler-job-collections-contributor) | Lehetővé teszi az Ütemező feladatgyűjtemények kezelését, de azok elérését nem. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Általános kérdések


### <a name="contributor"></a>Közreműködő

Lehetővé teszi, hogy az erőforrásokhoz való hozzáférést kivéve mindent kezeljen.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Szerepkörök, házirend-hozzárendelések, házirend-definíciók és házirendkészlet-definíciók törlése |
> | Microsoft.Authorization/*/Írás | Szerepkörök, szerepkör-hozzárendelések, házirend-hozzárendelések, házirend-definíciók és házirendkészlet-definíciók létrehozása |
> | Microsoft.Authorization/eevateAccess/Action | A hívónak felhasználói hozzáférésű rendszergazdai hozzáférést engedélyez a bérlői hatókörben |
> | Microsoft.Blueprint/blueprintAssignments/write | Tervrajzok hozzárendelésének létrehozása vagy frissítése |
> | Microsoft.Blueprint/blueprintAssignments/delete | Tervhozzárendelések törlése |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi, hogy mindent kezeljen, beleértve az erőforrásokhoz való hozzáférést is.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | * | Minden típusú erőforrás létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi, hogy mindent megtekint, de nem hajt végre módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Authorization/* | Engedélyezés kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Klasszikus virtuális gép közreműködője

Lehetővé teszi a klasszikus virtuális gépek kezelését, de nem férhet hozzájuk, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicCompute/domainNames/* | Klasszikus számítási tartománynevek létrehozása és kezelése |
> | Microsoft.ClassicCompute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Fenntartott ip-cím csatolása |
> | Microsoft.ClassicNetwork/reservedIps/read | Megkapja a fenntartott Ips |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozik a virtuális hálózathoz. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Szerezd meg a virtuális hálózatot. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfiók lemezét adja vissza. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | A tárfiók lemezképét adja vissza. (Elavult. A 'Microsoft.ClassicStorage/storageAccounts/vmImages) használata) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A tárfiókok hozzáférési kulcsainak listája. |
> | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a tárfiókot az adott fiókkal. |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
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

### <a name="virtual-machine-administrator-login"></a>Virtuálisgép-rendszergazda bejelentkezés

Virtuális gépek megtekintése a portálon, és bejelentkezés rendszergazdaként

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Nyilvános IP-cím definíciót kap. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.Network/loadBalancers/read | Betöltő definíciója |
> | Microsoft.Network/networkInterfaces/read | Hálózati kapcsolat definíciója.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés virtuális gépre normál felhasználóként |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre Windows rendszergazdai vagy Linux-gyökérfelhasználói jogosultságokkal |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi a virtuális gépek kezelését, de a hozzájuk való hozzáférést, és nem azt a virtuális hálózatot vagy tárfiókot, amelyhez csatlakoznak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/availabilitySets/* | Számítási rendelkezésre állási készletek létrehozása és kezelése |
> | Microsoft.Compute/locations/* | Számítási helyek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachines/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/virtualMachineScaleSets/* | Virtuális gépek létrehozása és kezelése |
> | Microsoft.Compute/disks/write | Új lemez létrehozása vagy meglévő frissítése |
> | Microsoft.Compute/disks/read | Lemez tulajdonságainak beszereznie |
> | Microsoft.Compute/disks/delete | A lemez törlése |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy alkalmazásátjáró-háttércímkészlethez csatlakozik. Nem riasztható. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Egy terheléselosztó háttérkészletéhez csatlakozik. Nem riasztható. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Egy bejövő NAT-készletbe érkező terheléselosztót egyesít. Nem lehet riasztani. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Egy bejövő nat-szabály hoz egy terheléselosztót. Nem riasztható. |
> | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi a terheléselosztó szondáinak használatát. Ezzel az engedéllyel például a virtuálisgép-méretezési készlet healthProbe tulajdonsága hivatkozhat a mintavételre. Nem lehet riasztani. |
> | Microsoft.Network/loadBalancers/read | Betöltő definíciója |
> | Microsoft.Network/locations/* | Hálózati helyek létrehozása és kezelése |
> | Microsoft.Network/networkInterfaces/* | Hálózati adapterek létrehozása és kezelése |
> | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoporthoz csatlakozik. Nem riasztható. |
> | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának becsatornája |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános IP-címhez csatlakozik. Nem riasztható. |
> | Microsoft.Network/publicIPAddresses/read | Nyilvános IP-cím definíciót kap. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.Network/virtualNetworks/alhálózatok/join/action | Virtuális hálózathoz csatlakozik. Nem riasztható. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági mentésvédelmi szándék létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági másolat létrehozása védett elem |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi házirendet visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Védelmi házirend létrehozása |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/write | A Vault-művelet létrehozása "tároló" típusú Azure-erőforrást hoz létre |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="virtual-machine-user-login"></a>Virtuálisgép felhasználói bejelentkezése

Tekintse meg a virtuális gépek a portálon, és jelentkezzen be, mint egy rendszeres felhasználó.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Network/publicIPAddresses/read | Nyilvános IP-cím definíciót kap. |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.Network/loadBalancers/read | Betöltő definíciója |
> | Microsoft.Network/networkInterfaces/read | Hálózati kapcsolat definíciója.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés virtuális gépre normál felhasználóként |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="networking"></a>Hálózat


### <a name="cdn-endpoint-contributor"></a>CDN végpont közreműködője

CdN-végpontok kezelésére, de nem adhat hozzáférést más felhasználóknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
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

### <a name="cdn-endpoint-reader"></a>CDN végpontolvasó

A CDN-végpontok megtekintése, de nem végezhet módosításokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
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

### <a name="cdn-profile-contributor"></a>CDN-profil közreműködője

A CDN-profilok és azok végpontjai kezelhetők, de nem adhatnak hozzáférést más felhasználóknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
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

### <a name="cdn-profile-reader"></a>CDN-profilolvasó

Megtekintheti a CDN-profilokat és azok végpontjait, de nem módosíthatja őket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
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

Lehetővé teszi a klasszikus hálózatok kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicNetwork/* | Klasszikus hálózatok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi a DNS-zónák és rekordkészletek kezelését az Azure DNS-ben, de nem teszi lehetővé, hogy szabályozhassa, ki férhet hozzá.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/dnsZones/* | DNS-zónák és rekordok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi a hálózatok kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/* | Hálózatok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi a Traffic Manager-profilok kezelését, de nem teszi lehetővé annak szabályozását, hogy ki férhet hozzájuk.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Avere közreműködő

Avere vFXT-fürt ötös fürtöt hozhat létre és kezelhet.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/disks/* |  |
> | Microsoft.Network/*/olvasás |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.Network/virtualNetworks/alhálózatok/olvasás | Virtuális hálózati alhálózat-definíció bekéselése |
> | Microsoft.Network/virtualNetworks/alhálózatok/join/action | Virtuális hálózathoz csatlakozik. Nem riasztható. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Erőforrás, például a tárfiók vagy az SQL-adatbázis egy alhálózathoz való egyesítése. Nem lehet riasztani. |
> | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoporthoz csatlakozik. Nem riasztható. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/*/olvasás |  |
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lekéri az erőforráscsoport erőforrásait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob törlésének eredményét adja eredményül. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Az Avere vFXT-fürt használja a fürt kezeléséhez

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beszereznie |
> | Microsoft.Network/networkInterfaces/read | Hálózati kapcsolat definíciója.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy egy meglévő hálózati illesztőt frissít.  |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.Network/virtualNetworks/alhálózatok/olvasás | Virtuális hálózati alhálózat-definíció bekéselése |
> | Microsoft.Network/virtualNetworks/alhálózatok/join/action | Virtuális hálózathoz csatlakozik. Nem riasztható. |
> | Microsoft.Network/networkSecurityGroups/join/action | Hálózati biztonsági csoporthoz csatlakozik. Nem riasztható. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Egy tároló törlésének eredményét adja eredményül. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | A tárolók listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blob-tároló eredményét adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob törlésének eredményét adja eredményül. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="backup-contributor"></a>Biztonsági másolat közreműködője

Lehetővé teszi a biztonsági mentési szolgáltatás kezelését, de nem hozhat létre tárolókat, és nem adhat hozzáférést másoknak

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | A biztonsági mentés kezelésével kapcsolatos működési eredmények kezelése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Biztonsági másolat tárolóinak létrehozása és kezelése a Recovery Services-tároló biztonsági másolataiban |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolólistát |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Biztonságimentési kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Biztonsági mentési házirendek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Biztonsági másolatot készíteni és kezelni |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Biztonsági másolatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Biztonsági másolatelemeit tartalmazó tárolók létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A helyreállítási szolgáltatások védett elemeinek és védett kiszolgálóinak összegzését adja vissza. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | A biztonsági mentéshez kapcsolódó tanúsítványok létrehozása és kezelése a Recovery Services-tárolóban |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | A tárolóval kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/olvasás | Lekéri a helyreállítási szolgáltatások tárolójának riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/usages/* | A Recovery Services-tároló használatának létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Művelet ellenőrzése védett cikken |
> | Microsoft.RecoveryServices/Vaults/write | A Vault-művelet létrehozása "tároló" típusú Azure-erőforrást hoz létre |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | A helyreállítási szolgáltatások tárolójának biztonsági mentési műveletállapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált összes biztonságimásolat-kezelési kiszolgálót visszaadja. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Minden védhető tároló beszerezhető |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási szolgáltatások tárolóinak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Szolgáltatások ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/írás | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveletállapot bekerülése egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentésvédelmi leképezés listázása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="backup-operator"></a>Biztonsági másolat operátora

Lehetővé teszi a biztonsági mentési szolgáltatások kezelését, kivéve a biztonsági mentés eltávolítását, a tároló létrehozását és a másokhoz való hozzáférést

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotát adja eredményül. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | A védelmi tárolón végrehajtott művelet eredménye. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése a védett elemhez. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | A védett elemeken végrehajtott művelet eredményének bekerülése. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja eredményül. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elem-helyreállítás kiépítése védett elemhez |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Helyreállítási pontok beszerzése a védett elemekhez. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | A védett elemek helyreállítási pontjainak visszaállítása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Azonnali elem-helyreállítás visszavonása védett elem esetén |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Biztonsági másolat létrehozása védett elem |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tárolót visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolólistát |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Biztonsági mentési feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Biztonságimentési kezelési műveletek eredményeinek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A házirend-művelet eredményeinek beszerezése. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi házirendet visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Biztonsági másolatot készíteni és kezelni |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A helyreállítási szolgáltatások védett elemeinek és védett kiszolgálóinak összegzését adja vissza. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az Erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás-/tároló hitelesítő tanúsítványát. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Kiterjesztett információ beszerezése művelet leveszi egy objektum kiterjesztett adatait, amely az Azure-erőforrástípusát ?vault? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Kiterjesztett információ beszerezése művelet leveszi egy objektum kiterjesztett adatait, amely az Azure-erőforrástípusát ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/olvasás | Lekéri a helyreállítási szolgáltatások tárolójának riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelet használható a művelet állapotának és az aszinkron módon elküldött művelet eredményének lekérnie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A Get Containers művelet használható lekérni egy erőforrás regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A Register Service Container művelet segítségével regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Művelet ellenőrzése védett cikken |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | A helyreállítási szolgáltatások tárolójának biztonsági mentési műveletállapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | A házirend-művelet állapotának beírása. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Regisztrált tároló létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Lekérdezés a tárolón belüli számítási feladatokhoz |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált összes biztonságimásolat-kezelési kiszolgálót visszaadja. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági mentésvédelmi szándék létrehozása |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági mentés védelmi leképezése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Minden védhető tároló beszerezhető |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Az összes elem beszereznie egy tárolóba |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási szolgáltatások tárolóinak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Szolgáltatások ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/írás | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveletállapot bekerülése egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentésvédelmi leképezés listázása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="backup-reader"></a>Biztonsági másolat olvasója

Megtekintheti a biztonsági mentési szolgáltatásokat, de nem módosíthatja

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | A művelet állapotát adja eredményül. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | A védelmi tárolón végrehajtott művelet eredménye. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | A védett elemeken végrehajtott művelet eredményének bekerülése. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja eredményül. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett elem objektumadatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Helyreállítási pontok beszerzése a védett elemekhez. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tárolót visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja eredményül. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Az összes feladatobjektumot visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Visszaadja a helyreállítási szolgáltatások tárolójának biztonsági mentési műveletének eredményét. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | A házirend-művelet eredményeinek beszerezése. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Az összes védelmi házirendet visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Az összes védett elem listáját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót visszaadja |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | A helyreállítási szolgáltatások védett elemeinek és védett kiszolgálóinak összegzését adja vissza. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Kiterjesztett információ beszerezése művelet leveszi egy objektum kiterjesztett adatait, amely az Azure-erőforrástípusát ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/olvasás | Lekéri a helyreállítási szolgáltatások tárolójának riasztásait. |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelet használható a művelet állapotának és az aszinkron módon elküldött művelet eredményének lekérnie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A Get Containers művelet használható lekérni egy erőforrás regisztrált tárolók. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | A Recovery Services Vault tárolási konfigurációját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | A Helyreállítási szolgáltatások tárolójának konfigurációját adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | A helyreállítási szolgáltatások tárolójának biztonsági mentési műveletállapotát adja vissza. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | A házirend-művelet állapotának beírása. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | A tárolóban regisztrált összes biztonságimásolat-kezelési kiszolgálót visszaadja. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági mentés védelmi leképezése |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Az összes elem beszereznie egy tárolóba |
> | Microsoft.RecoveryServices/locations/backupStatus/action | A helyreállítási szolgáltatások tárolóinak biztonsági mentési állapotának ellenőrzése |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/írás | A riasztás feloldása. |
> | Microsoft.RecoveryServices/operations/read | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Műveletállapot bekerülése egy adott művelethez |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentésvédelmi leképezés listázása |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Szolgáltatások ellenőrzése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
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

### <a name="classic-storage-account-contributor"></a>Klasszikus tárfiók közreműködője

Lehetővé teszi a klasszikus tárfiókok kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicStorage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="classic-storage-account-key-operator-service-role"></a>Klasszikus tárfiókkulcs-operátori szolgáltatás szerepkör

A klasszikus tárfiókkulcs-operátorok felsorolhatják és újragenerálhatják a kulcsokat a klasszikus tárfiókokban

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | A tárfiókok hozzáférési kulcsainak listája. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Újragenerálja a tárfiók meglévő hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="data-box-contributor"></a>Adatmező közreműködője

Lehetővé teszi, hogy mindent kezeljen a Data Box Szolgáltatás alatt, kivéve mások hozzáférését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="data-box-reader"></a>Adatdoboz-olvasó

Lehetővé teszi a Data Box Szolgáltatás kezelését, kivéve a sorrend létrehozását vagy a rendelés részleteinek szerkesztését, valamint a másokhoz való hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Databox/*/olvasás |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | A rendeléshez kapcsolódó titkosítatlan hitelesítő adatok listája. |
> | Microsoft.Databox/locations/availableSkus/action | Ez a módszer a rendelkezésre álló skus listáját adja vissza. |
> | Microsoft.Databox/locations/validateInputs/action | Ez a módszer minden típusú ellenőrzést végez. |
> | Microsoft.Databox/locations/regionConfiguration/action | Ez a módszer a régió konfigurációit adja vissza. |
> | Microsoft.Databox/locations/validateAddress/action | Ellenőrzi a szállítási címet, és alternatív címeket ad meg, ha vannak ilyenek. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="data-lake-analytics-developer"></a>A Data Lake Analytics fejlesztője

Lehetővé teszi a saját feladatok küldését, figyelését és kezelését, de a Data Lake Analytics-fiókok létrehozását és törlését nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BigAnalytics/fiókok/* |  |
> | Microsoft.DataLakeAnalytics/fiókok/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/fiókok/Törlés | DataLakeAnalytics-fiók törlése. |
> | Microsoft.DataLakeAnalytics/fiókok/TakeOwnership/action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Microsoft.DataLakeAnalytics/accounts/Write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | DataLakeAnalytics-fiók összekapcsolt DataLakeStore-fiókjának létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | A DataLakeStore-fiók leválasztása DataLakeAnalytics-fiókról. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | DataLakeAnalytics-fiók összekapcsolt tárfiókjának létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Storage-fiók leválasztása DataLakeAnalytics-fiókról. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Tűzfalszabály létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Tűzfalszabály törlése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Számítási szabályzat létrehozása vagy frissítése. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Számítási szabályzat törlése. |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="reader-and-data-access"></a>Olvasó- és adathozzáférés

Lehetővé teszi, hogy mindent megtekint, de nem teszi lehetővé, hogy törölje, vagy hozzon létre egy tárfiókot vagy zárt erőforrást. Emellett lehetővé teszi az olvasási/írási hozzáférést a tárfiókban található összes adathoz a tárfiók kulcsaihoz való hozzáférés révén.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | Microsoft.Storage/storageAccounts/ListAccountsSas/action | A megadott tárfiók SAS-jogkivonatát adja vissza. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-account-contributor"></a>Tárfiók-közreműködő

Lehetővé teszi a tárfiókok kezelését. Hozzáférést biztosít a fiókkulcshoz, amellyel a megosztott kulcs hitelesítésével lehet hozzáférni az adatokhoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Az Analysis Server diagnosztikai beállításának létrehozása, frissítése vagy beolvasása |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Erőforrás, például a tárfiók vagy az SQL-adatbázis egy alhálózathoz való egyesítése. Nem lehet riasztani. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/* | Tárfiókok létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-account-key-operator-service-role"></a>Tárfiókkulcs-operátor szolgáltatásszerepköre

Lehetővé teszi a tárfiók hozzáférési kulcsainak listázását és újragenerálását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Újragenerálja a megadott tárfiók hozzáférési kulcsait. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-blob-data-contributor"></a>Storage Blob-adatközreműködő

Azure Storage-tárolók és -blobok olvasása, írása és törlése. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Tároló törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Visszaad egy tárolót vagy a tárolók listáját. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Módosítsa a tároló metaadatait vagy tulajdonságait. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDeKey/action | A Blob szolgáltatás felhasználói átadási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Blob törlése. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob vagy blobok listájának visszaadása. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Írjon egy blobba. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-blob-data-owner"></a>Tárolási blob-adatok tulajdonosa

Teljes hozzáférést biztosít az Azure Storage blobtárolóihoz és adataihoz, beleértve a POSIX hozzáférés-vezérlés hozzárendelését is. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Teljes engedélyek a tárolókhoz. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDeKey/action | A Blob szolgáltatás felhasználói átadási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Teljes engedélyek a blobok. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-blob-data-reader"></a>Tárolási blob adatolvasó

Az Azure Storage-tárolók és -blobok olvasása és listázása. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Visszaad egy tárolót vagy a tárolók listáját. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDeKey/action | A Blob szolgáltatás felhasználói átadási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blob vagy blobok listájának visszaadása. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-blob-delegator"></a>Tárolási blob delegator

Szerezzen be egy felhasználói delegálási kulcsot, amely ezután egy Azure AD-hitelesítő adatokkal aláírt tároló vagy blob megosztott hozzáférési aláírásának létrehozásához használható. További információ: [Felhasználói delegáláslétrehozása SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDeKey/action | A Blob szolgáltatás felhasználói átadási kulcsát adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-file-data-smb-share-contributor"></a>Tárolófájl-adat SMB-megosztás közreműködője

Lehetővé teszi az Azure-fájlmegosztásokban lévő fájlok/könyvtárak olvasását, írását és törlését. Ez a szerepkör nem rendelkezik beépített megfelelővel a Windows fájlkiszolgálókon.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Fájl/mappa vagy fájlok/mappák listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Fájl írásának vagy mappa létrehozásának eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Fájl/mappa törlésének eredményét adja eredményül. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Storage File Data SMB share emelt szintű közreműködő

Lehetővé teszi az Azure-fájlmegosztások fájljainak és könyvtáraihoz tartozó ACL-ek olvasását, írását, törlését és módosítását. Ez a szerepkör egyenértékű a Windows fájlkiszolgálókon a módosítás fájlmegosztási acl-jével.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Fájl/mappa vagy fájlok/mappák listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Fájl írásának vagy mappa létrehozásának eredményét adja vissza. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Fájl/mappa törlésének eredményét adja eredményül. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Fájlra/mappára vonatkozó engedély módosításának eredményét adja eredményül. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-file-data-smb-share-reader"></a>Tárolófájl-adat SMB-megosztásolvasó

Lehetővé teszi az olvasási hozzáférést a fájlokhoz/könyvtárakhoz az Azure-fájlmegosztásokban. Ez a szerepkör egyenértékű a Windows fájlkiszolgálókon olvasott fájlmegosztási acl-vel.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Fájl/mappa vagy fájlok/mappák listáját adja vissza. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-queue-data-contributor"></a>Tárolási várólista adatközreműködője

Az Azure Storage-várólisták és várólista-üzenetek olvasása, írása és törlése. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Várólista törlése. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólistát vagy várólisták listáját adja vissza. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Várólista-metaadatok vagy -tulajdonságok módosítása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Egy vagy több üzenet törlése a várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Bepillantás vagy beolvasás egy várólistából. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet hozzáadása várólistához. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-queue-data-message-processor"></a>Tárolóvárólista adatüzenet-feldolgozója

Betekintés, beolvasása és törlése egy üzenetet egy Azure Storage-várólistából. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Kukkéssz egy üzenet. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet beolvasása és törlése. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-queue-data-message-sender"></a>Tárolási várólista-adatüzenet-küldő

Üzenetek hozzáadása egy Azure Storage-várólistához. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadása várólistához. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="storage-queue-data-reader"></a>Tárolóvárólista adatolvasója

Az Azure Storage-várólisták és várólista-üzenetek olvasása és listázása. Ha meg szeretné tudni, hogy egy adott adatművelethez milyen műveletek szükségesek, olvassa el [a Blob- és várólista-adatok hívásának engedélyei című témakört.](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Várólistát vagy várólisták listáját adja vissza. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Bepillantás vagy beolvasás egy várólistából. |
> | **NotDataActions** |  |
> | *nincs* |  |

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


### <a name="azure-maps-data-reader-preview"></a>Azure Maps adatolvasó (előzetes verzió)

Hozzáférést biztosít az Azure maps-fióktérképekkel kapcsolatos adatok olvasásához.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Maps/accounts/data/read | Olvasási hozzáférést biztosít egy térképfiókhoz. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="search-service-contributor"></a>Keresési szolgáltatás közreműködője

Lehetővé teszi a Keresési szolgáltatások kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Search/searchServices/* | Keresési szolgáltatások létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="web-plan-contributor"></a>Webes terv közreműködője

Lehetővé teszi a webhelyek webes csomagjainak kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/serverFarms/* | Kiszolgálófarmok létrehozása és kezelése |
> | Microsoft.Web/hostingEnvironments/Csatlakozás/Művelet | Alkalmazásszolgáltatási környezethez csatlakozik |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="website-contributor"></a>Webhely közreműködője

Lehetővé teszi a webhelyek kezelését (nem webes sémákat), de nem férhet hozzájuk.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az Insights-összetevők létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/certificates/* | Webhelytanúsítványok létrehozása és kezelése |
> | Microsoft.Web/listSites AssignedToHostName/read | Az állomásnévhez rendelt webhelyek nevének leadása. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beszereznie |
> | Microsoft.Web/sites/* | Webhelyek létrehozása és kezelése (a webhely-létrehozáshoz írási engedélyek szükségesek a társított App Service-csomaghoz) |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

acr törlés

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/összetevők/törlés | A tárolók beállításjegyzékében lévő műtermék törlése. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

acr kép aláírója

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/aláírás/írás | Tartalom leküldése/lekérése a tárolóbeállításjegyzék metaadatainak leküldése/lekérése. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="acrpull"></a>AcrPull között

acr húzás

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/lekérése/olvasás | Képek lekérése vagy lekérése egy tároló rendszerleíró adatbázisából. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="acrpush"></a>AcrPush között

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/lekérése/olvasás | Képek lekérése vagy lekérése egy tároló rendszerleíró adatbázisából. |
> | Microsoft.ContainerRegistry/regisztika/push/write | Lemezképek leküldése vagy írása egy tároló beállításjegyzékébe. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

acr karantén adatolvasó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/karantén/olvasás | Karanténba helyezett lemezképek lekérése vagy lekérése a tárolóbeállításjegyzékből |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

acr karantén adatíró

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerRegistry/regisztika/karantén/olvasás | Karanténba helyezett lemezképek lekérése vagy lekérése a tárolóbeállításjegyzékből |
> | Microsoft.ContainerRegistry/regisztika/karantén/írás | Karanténban lévő képek karanténállapotának írása/módosítása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes szolgáltatásfürt felügyeleti szerepkör

Fürtfelügyeleti hitelesítő adatok műveletének listázása.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Felügyelt fürt fürtrendszergazdai hitelesítő adatainak listázása |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Felügyelt fürthozzáférési profil beszereznie szerepkörnév szerint listahitelesítő adatok használatával |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-kubernetes-service-cluster-user-role"></a>Azure Kubernetes szolgáltatásfürt felhasználói szerepköre

Fürtfelhasználói hitelesítő adatokkal kapcsolatos műveletének listázása.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Felügyelt fürt fürtfelhasználói hitelesítő adatainak listázása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB-fiókolvasó szerepkör

Az Azure Cosmos DB-fiók adatai olvasása. Az Azure Cosmos DB-fiókok kezeléséhez tekintse meg a [DocumentDB-fiók közreműködőjét.](#documentdb-account-contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DocumentDB/*/olvasás | Bármilyen gyűjtemény elolvasása |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Beolvassa az adatbázisfiók írásvédett kulcsait. |
> | Microsoft.Insights/MetricDefinitions/read | Metrikadefiníciók olvasása |
> | Microsoft.Insights/Metrics/read | Mérőszámok olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az Azure Cosmos DB-fiókok kezelését, de a bennük lévő adatok elérését nem. Megakadályozza a fiókkulcsok hoz és a kapcsolati karakterláncokhoz való hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Erőforrás, például a tárfiók vagy az SQL-adatbázis egy alhálózathoz való egyesítése. Nem lehet riasztani. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Cosmos DB-adatbázis vagy egy fiók tárolójának visszaállítási kérelmet küldhet

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Biztonsági másolat konfigurálására vonatkozó kérelem elküldése |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Visszaállítási kérelem küldése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="documentdb-account-contributor"></a>DocumentDB-fiók közreműködője

Az Azure Cosmos DB-fiókok kezelése. Az Azure Cosmos DB korábbi nevén DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DocumentDb/databaseAccounts/* | Azure Cosmos DB-fiókok létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Erőforrás, például a tárfiók vagy az SQL-adatbázis egy alhálózathoz való egyesítése. Nem lehet riasztani. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="redis-cache-contributor"></a>Redis gyorsítótár közreműködője

Lehetővé teszi a Redis-gyorsítótárak kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Cache/redis/* | Redis-gyorsítótárak létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="sql-db-contributor"></a>SQL DB közreműködő

Lehetővé teszi az SQL-adatbázisok kezelését, de a hozzáférésüket nem. Emellett nem kezelheti a biztonsággal kapcsolatos házirendeket vagy a szülő SQL-kiszolgálókat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | SQL-adatbázisok létrehozása és kezelése |
> | Microsoft.Sql/servers/read | Adja vissza a kiszolgálók listáját, vagy lekéri a megadott kiszolgáló tulajdonságait. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Insights/metrics/read | Mérőszámok olvasása |
> | Microsoft.Insights/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
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
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázisblob-naplózási rekordok lekérése |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Kapcsolatházirendek szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Adatmaszkolási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Biztonsági riasztási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Biztonsági mutatók szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az SQL felügyelt példányok és a szükséges hálózati konfiguráció kezelését, de nem adhat hozzáférést másoknak.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/alhálózatok/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/metrics/read | Mérőszámok olvasása |
> | Microsoft.Insights/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
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

### <a name="sql-security-manager"></a>SQL biztonsági kezelő

Lehetővé teszi az SQL-kiszolgálók és adatbázisok biztonsággal kapcsolatos házirendjeinek kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Erőforrás, például a tárfiók vagy az SQL-adatbázis egy alhálózathoz való egyesítése. Nem lehet riasztani. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL-kiszolgálónaplózási házirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/auditingSettings/* | SQL-kiszolgáló naplózási beállításának létrehozása és kezelése |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Adott kiszolgálón konfigurált kiterjesztett kiszolgálóblob-naplózási házirend részleteinek lekérése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL-kiszolgálóadatbázis-naplózási házirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL-kiszolgálóadatbázis-naplózási beállításainak létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázisblob-naplózási rekordok lekérése |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL-kiszolgálóadatbázis-kapcsolatházirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL-kiszolgálói adatmaszkolási házirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Egy adott adatbázisban konfigurált kiterjesztett blob naplózási házirend részleteinek lekérése |
> | Microsoft.Sql/servers/databases/read | Adja vissza az adatbázisok listáját, vagy lekéri a megadott adatbázis tulajdonságait. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Adatbázisséma beszerezni. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Adatbázisoszlop beszerezni. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Adatbázistábla beszerezni. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL server adatbázis biztonsági riasztási házirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL-kiszolgálóadatbázis biztonsági mutatóinak létrehozása és kezelése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Adja vissza a kiszolgálók listáját, vagy lekéri a megadott kiszolgáló tulajdonságait. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL-kiszolgálóbiztonsági riasztási házirendek létrehozása és kezelése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az SQL-kiszolgálók és adatbázisok kezelését, de a hozzáférésüket nem, és nem a biztonsággal kapcsolatos házirendeket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | SQL-kiszolgálók létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Insights/metrics/read | Mérőszámok olvasása |
> | Microsoft.Insights/metricDefinitions/read | Metrikadefiníciók olvasása |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | SQL-kiszolgáló naplózási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/auditingSettings/* | SQL-kiszolgáló naplózási beállításainak szerkesztése |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | SQL-kiszolgálóadatbázis-naplózási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/auditingSettings/* | SQL-kiszolgálóadatbázis-naplózási beállítások szerkesztése |
> | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázisblob-naplózási rekordok lekérése |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | SQL-kiszolgáló adatbázis-kapcsolatházirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | SQL-kiszolgálóadatbázis-adatmaszkolási házirendek szerkesztése |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | SQL-kiszolgálóadatbázis biztonsági riasztási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/databases/securityMetrics/* | SQL-kiszolgáló adatbázisának biztonsági mutatóinak szerkesztése |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | SQL-kiszolgáló biztonsági riasztási házirendjeinek szerkesztése |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="analytics"></a>Elemzés


### <a name="azure-event-hubs-data-owner"></a>Az Azure Event Hubs adatainak tulajdonosa

Lehetővé teszi az Azure Event Hubs-erőforrások teljes körű elérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az Azure Event Hubs-erőforrások fogadását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-event-hubs-data-sender"></a>Az Azure Event Hubs adatküldő

Lehetővé teszi a hozzáférést az Azure Event Hubs erőforrásaihoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.EventHub/*/send/action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="data-factory-contributor"></a>Adatfeldolgozó közreműködő

Adatgyárakat, valamint gyermekerőforrásokat hozhat létre és kezelhet.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.DataFactory/dataFactories/* | Adatgyárak és gyermekerőforrások létrehozása és kezelése. |
> | Microsoft.DataFactory/gyárak/* | Adatgyárak és gyermekerőforrások létrehozása és kezelése. |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.EventGrid/eventSubscriptions/write | EventSubscription létrehozása vagy frissítése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="data-purger"></a>Adatpurger

Az elemzési adatok törlése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Adatok kiürítése az Application Insightsból |
> | Microsoft.OperationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="hdinsight-cluster-operator"></a>HDInsight fürtoperátor

Lehetővé teszi a HDInsight-fürtkonfigurációk olvasását és módosítását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/clusters/getGatewaySettings/action | A HDInsight-fürt átjáróbeállításainak beszereznie |
> | Microsoft.HDInsight/clusters/updateGatewaySettings/action | A HDInsight-fürt átjáróbeállításainak frissítése |
> | Microsoft.HDInsight/clusters/configurations/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/deployments/operations/read | Leésezése vagy listázása a telepítési műveleteket. |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="hdinsight-domain-services-contributor"></a>A HDInsight tartományi szolgáltatások közreműködője

A HDInsight vállalati biztonsági csomaghoz szükséges tartományszolgáltatásokolvasás, -létrehozás, -módosítás és -törlés

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.AAD/*/olvasás |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

A Log Analytics közreműködője letudja olvasni az összes figyelési adatot, és szerkesztheti a figyelési beállításokat. A figyelési beállítások szerkesztése magában foglalja a virtuális gépbővítmény virtuális gépekhez való hozzáadását; a tárfiók kulcsainak olvasása az Azure Storage-ból származó naplók gyűjteményének konfigurálásához; Automatizálási fiókok létrehozása és konfigurálása; megoldások hozzáadása; és konfigurálja az Azure-diagnosztikát az összes Azure-erőforráson.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A tárfiókok hozzáférési kulcsainak listája. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Az Analysis Server diagnosztikai beállításának létrehozása, frissítése vagy beolvasása |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

A Log Analytics Reader megtekintheti és keresheti az összes figyelési adatot, valamint megtekintheti a figyelési beállításokat, beleértve az Azure-diagnosztika konfigurációjának megtekintését az összes Azure-erőforráson.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motorral. |
> | Microsoft.OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/munkaterületek/sharedKeys/read | A munkaterület megosztott kulcsainak beolvasása. Ezek a kulcsok a Microsoft Operational Insights-ügynökök és a munkaterület összekapcsolására szolgálnak. |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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


### <a name="blockchain-member-node-access-preview"></a>Blokklánc-tagcsomópont-hozzáférés (előzetes verzió)

Lehetővé teszi a hozzáférést a Blockchain tagcsomópontjaihoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | A meglévő blokklánc-tagtranzakció-csomópont(ok) bekerülése vagy listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Egy blokklánc-tag tranzakciócsomóponthoz csatlakozik. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="ai--machine-learning"></a>AI + gépi tanulás


### <a name="cognitive-services-contributor"></a>Kognitív szolgáltatások közreműködője

Lehetővé teszi a Cognitive Services kulcsainak létrehozását, olvasását, frissítését, törlését és kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Leveszi az előfizetés funkcióit. |
> | Microsoft.Features/providers/features/read | Egy adott erőforrás-szolgáltató előfizetési funkciójának beszerzése. |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/diagnosticSettings/* | Az Analysis Server diagnosztikai beállításának létrehozása, frissítése vagy beolvasása |
> | Microsoft.Insights/logDefinitions/read | Naplódefiníciók olvasása |
> | Microsoft.Insights/metricdefinitions/read | Metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Mérőszámok olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/deployments/operations/read | Leésezése vagy listázása a telepítési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beszerezése. |
> | Microsoft.Resources/subscriptions/read | Leveszi az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="cognitive-services-data-reader-preview"></a>Cognitive Services adatolvasó (előzetes verzió)

Lehetővé teszi a Cognitive Services-adatok olvasását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.CognitiveServices/*/olvasás |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="cognitive-services-user"></a>Kognitív szolgáltatások felhasználója

Lehetővé teszi a Cognitive Services kulcsainak olvasását és listázását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.CognitiveServices/*/olvasás |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Listabillentyűk |
> | Microsoft.Insights/alertRules/read | Klasszikus metrikariasztás olvasása |
> | Microsoft.Insights/diagnosticSettings/read | Erőforrás-diagnosztikai beállítás olvasása |
> | Microsoft.Insights/logDefinitions/read | Naplódefiníciók olvasása |
> | Microsoft.Insights/metricdefinitions/read | Metrikadefiníciók olvasása |
> | Microsoft.Insights/metrics/read | Mérőszámok olvasása |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/operations/read | Leésezése vagy listázása a telepítési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beszerezése. |
> | Microsoft.Resources/subscriptions/read | Leveszi az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="mixed-reality"></a>Vegyes valóság


### <a name="spatial-anchors-account-contributor"></a>Területi horgonyok fiók közreműködője

Lehetővé teszi a fiók térbeli horgonyok kezelését, de törlését nem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Fedezze fel a közeli térbeli horgonyokat |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beszereznie |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok megkeresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok küldése az Azure Spatial Anchors szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/írás | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="spatial-anchors-account-owner"></a>Térbeli horgonyok fiók tulajdonosa

Lehetővé teszi a fiók térbeli horgonyok kezelését, beleértve azok törlését is

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Fedezze fel a közeli térbeli horgonyokat |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beszereznie |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok megkeresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok küldése az Azure Spatial Anchors szolgáltatás minőségének javítása érdekében |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/írás | Térbeli horgonyok tulajdonságainak frissítése |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="spatial-anchors-account-reader"></a>Térbeli horgonyok fiókolvasó

Lehetővé teszi a térbeli horgonyok tulajdonságainak megkeresését és olvasását a fiókban

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Fedezze fel a közeli térbeli horgonyokat |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beszereznie |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok megkeresése |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatok küldése az Azure Spatial Anchors szolgáltatás minőségének javítása érdekében |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="integration"></a>Integráció


### <a name="api-management-service-contributor"></a>API-felügyeleti szolgáltatás közreműködője

Kezelheti a szolgáltatást és az API-kat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/* | API Management szolgáltatás létrehozása és kezelése |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="api-management-service-operator-role"></a>API-felügyeleti szolgáltatás operátori szerepköre

Kezelheti a szolgáltatást, de az API-kat nem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API-felügyeleti szolgáltatáspéldányainak olvasása |
> | Microsoft.ApiManagement/service/backup/action | Biztonsági másolat API-kezelési szolgáltatása a megadott tárolóhoz egy felhasználó által megadott tárfiókban |
> | Microsoft.ApiManagement/service/delete | API-felügyeleti szolgáltatáspéldány törlése |
> | Microsoft.ApiManagement/service/managedeployments/action | Termékváltozat/egységek módosítása, az API Management Service területi telepítéseinek hozzáadása/eltávolítása |
> | Microsoft.ApiManagement/service/read | Metaadatok olvasása API-felügyeleti szolgáltatáspéldányhoz |
> | Microsoft.ApiManagement/service/restore/action | API-felügyeleti szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott tárfiókban |
> | Microsoft.ApiManagement/service/updatecertificate/action | SSL-tanúsítvány feltöltése API-felügyeleti szolgáltatáshoz |
> | Microsoft.ApiManagement/service/updatehostname/action | API-felügyeleti szolgáltatás egyéni tartományneveinek beállítása, frissítése vagy eltávolítása |
> | Microsoft.ApiManagement/service/write | API-felügyeleti szolgáltatáspéldány létrehozása vagy frissítése |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz társított kulcsok beszereznie |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="api-management-service-reader-role"></a>API-kezelés szolgáltatásolvasó szerepkör

Írásvédett hozzáférés a szolgáltatáshoz és az API-khoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ApiManagement/service/*/read | API-felügyeleti szolgáltatáspéldányainak olvasása |
> | Microsoft.ApiManagement/service/read | Metaadatok olvasása API-felügyeleti szolgáltatáspéldányhoz |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz társított kulcsok beszereznie |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="app-configuration-data-owner"></a>Alkalmazáskonfigurációs adatok tulajdonosa

Lehetővé teszi az alkalmazáskonfigurációs adatok teljes körű elérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | Microsoft.AppConfiguration/configurationStores/*/write |  |
> | Microsoft.AppConfiguration/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="app-configuration-data-reader"></a>Alkalmazáskonfigurációs adatolvasó

Lehetővé teszi az olvasási hozzáférést az alkalmazáskonfigurációs adatokhoz.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | *nincs* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.AppConfiguration/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-service-bus-data-owner"></a>Az Azure Service Bus adatainak tulajdonosa

Lehetővé teszi az Azure Service Bus erőforrásainak teljes körű elérését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus adatfogadó

Lehetővé teszi az Azure Service Bus-erőforrások fogadását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-service-bus-data-sender"></a>Az Azure Service Bus adatszolgáltatója

Lehetővé teszi az Azure Service Bus-erőforrások hoz való hozzáférés küldését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft.ServiceBus/*/topics/read |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-stack-registration-owner"></a>Az Azure Stack regisztrációs tulajdonosa

Lehetővé teszi az Azure Stack-regisztrációk kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.AzureStack/registrations/products/*/action |  |
> | Microsoft.AzureStack/registrations/products/read | Az Azure Stack Marketplace-termék tulajdonságainak beszerzése |
> | Microsoft.AzureStack/registrations/read | Az Azure Stack-regisztráció tulajdonságainak beírása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription közreműködő

Lehetővé teszi az EventGrid esemény-előfizetési műveletek kezelését.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések listázása témakörtípus szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása témakörtípus szerint |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az EventGrid esemény-előfizetések olvasását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription olvasása |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Globális esemény-előfizetések listázása témakörtípus szerint |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása témakörtípus szerint |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="intelligent-systems-account-contributor"></a>Intelligens rendszerek fiók közreműködője

Lehetővé teszi az Intelligens rendszerek fiókjainak kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.IntelligentSystems/fiókok/* | Intelligens rendszerfiókok létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="logic-app-contributor"></a>Logikai alkalmazás közreműködője

Lehetővé teszi a logikai alkalmazások kezelését, de a hozzáférés módosítását nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | A tárfiókok hozzáférési kulcsainak listája. |
> | Microsoft.ClassicStorage/storageAccounts/read | Adja vissza a tárfiókot az adott fiókkal. |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Az Analysis Server diagnosztikai beállításának létrehozása, frissítése vagy beolvasása |
> | Microsoft.Insights/logdefinitions/* | Ez az engedély szükséges azoknak a felhasználóknak, akiknek a portálon keresztül hozzáférésre van szükségük a tevékenységnaplókhoz. A naplókategóriák listázása a Tevékenységnaplóban. |
> | Microsoft.Insights/metricDefinitions/* | Metrikadefiníciók olvasása (egy erőforrás elérhető metrikatípusainak listája). |
> | Microsoft.Logic/* | A Logic Apps-erőforrások kezelése. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beszerezése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/connectionGateways/* | Kapcsolatátjáró létrehozása és kezelése. |
> | Microsoft.Web/connections/* | Kapcsolat létrehozása és kezelése. |
> | Microsoft.Web/customApis/* | Egyéni API-t hoz létre és kezel. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beszereznie |
> | Microsoft.Web/sites/functions/listSecrets/action | Függvénytitkok listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="logic-app-operator"></a>Logikai alkalmazás operátora

Lehetővé teszi a logikai alkalmazások olvasását, engedélyezését és letiltását, de nem szerkesztheti és frissítheti azokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/*/read | Az Insights-riasztások szabályainak olvasása |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Diagnosztikai beállítások bekerülése a Logikai alkalmazásokhoz |
> | Microsoft.Insights/metricDefinitions/*/read | A logic apps elérhető metrikák lekéri. |
> | Microsoft.Logic/*/olvasás | A Logic Apps erőforrásait olvassa. |
> | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Microsoft.Logic/workflows/validate/action | Ellenőrzi a munkafolyamatot. |
> | Microsoft.Resources/deployments/operations/read | Leésezése vagy listázása a telepítési műveleteket. |
> | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek beszerezése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Web/connectionGateways/*/read | Olvassa el a kapcsolatátjárókat. |
> | Microsoft.Web/connections/*/read | Olvassa el a Kapcsolatok at. |
> | Microsoft.Web/customApis/*/read | Egyéni API olvasása. |
> | Microsoft.Web/serverFarms/read | Az App Service-csomag tulajdonságainak beszereznie |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="identity"></a>Identitás


### <a name="managed-identity-contributor"></a>Felügyelt identitás közreműködője

Felhasználó hozzárendelt identitásának létrehozása, olvasása, frissítése és törlése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Meglévő felhasználó által hozzárendelt identitás bekéselése |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Új felhasználó hoz létre hozzárendelt identitást, vagy frissíti a meglévő, hozzárendelt identitáshoz társított címkéket |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="managed-identity-operator"></a>Felügyelt identitásoperátor

Felhasználó hozrendelt identitásának olvasása és hozzárendelése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="security"></a>Biztonság


### <a name="azure-sentinel-contributor"></a>Az Azure Sentinel közreműködője

Az Azure Sentinel közreműködője

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.SecurityInsights/* |  |
> | Microsoft.OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motorral. |
> | Microsoft.OperationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Kilépés az OMS-megoldásból |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület adatai között |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/*/olvasás |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Adatforrások beszereznie egy munkaterület alatt. |
> | Microsoft.Insights/munkafüzetek/* |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="azure-sentinel-reader"></a>Azure Sentinel olvasó

Azure Sentinel olvasó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motorral. |
> | Microsoft.OperationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.OperationalInsights/munkaterületek/LinkedServices/read | Csatolt szolgáltatások beszereznie az adott munkaterület alatt. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Mentett keresési lekérdezés bementése |
> | Microsoft.OperationsManagement/solutions/read | Kilépés az OMS-megoldásból |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület adatai között |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/*/olvasás |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Adatforrások beszereznie egy munkaterület alatt. |
> | Microsoft.Insights/munkafüzetek/olvasás | Munkafüzet olvasása |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
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

### <a name="azure-sentinel-responder"></a>Azure Sentinel válaszadó

Azure Sentinel válaszadó

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.SecurityInsights/*/read |  |
> | Microsoft.SecurityInsights/cases/* |  |
> | Microsoft.SecurityInsights/incidents/* |  |
> | Microsoft.OperationalInsights/munkaterületek/elemzés/lekérdezés/művelet | Keresés az új motorral. |
> | Microsoft.OperationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Adatforrások beszereznie egy munkaterület alatt. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read | Mentett keresési lekérdezés bementése |
> | Microsoft.OperationsManagement/solutions/read | Kilépés az OMS-megoldásból |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület adatai között |
> | Microsoft.OperationalInsights/munkaterületek/lekérdezés/*/olvasás |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read | Adatforrások beszereznie egy munkaterület alatt. |
> | Microsoft.Insights/munkafüzetek/olvasás | Munkafüzet olvasása |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
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

### <a name="key-vault-contributor"></a>Kulcstartó közreműködője

Lehetővé teszi a kulcstartók kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállítható kulcstartó kiürítése |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, szerkesztheti a biztonsági házirendeket, megtekintheti a riasztásokat és javaslatokat, elvetheti a riasztásokat és a javaslatokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Authorization/policyAssignments/* | Házirend-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policyDefinitions/* | Házirend-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policySetDefinitions/* | Házirendkészletek létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | Microsoft.operationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="security-assessment-contributor"></a>Biztonsági értékelés közreműködője

Lehetővé teszi az értékelések leküldése a Security Centerbe

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Security/assessments/write | Biztonsági értékelések létrehozása vagy frissítése az előfizetésen |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="security-manager-legacy"></a>Biztonsági kezelő (örökölt)

Ez egy örökölt szerep. Használja inkább a Biztonsági rendszergazda használatát.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.ClassicCompute/*/read | Klasszikus virtuális gépek konfigurációs információk olvasása |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Írási konfiguráció klasszikus virtuális gépekhez |
> | Microsoft.ClassicNetwork/*/olvasás | A klasszikus hálózatkonfigurációs információk elolvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Security/* | Biztonsági összetevők és házirendek létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Megtekintheti a javaslatokat és riasztásokat, megtekintheti a biztonsági házirendeket, megtekintheti a biztonsági állapotokat, de nem módosíthatja azokat.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.operationalInsights/munkaterületek/*/olvasás | Naplóelemzési adatok megtekintése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Security/*/olvasás | Biztonsági összetevők és házirendek olvasása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs felhasználó

Lehetővé teszi a virtuális gépek csatlakoztatását, indítását, újraindítását és leállítását az Azure DevTest Labs ben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Compute/availabilitySets/read | Rendelkezésre állási csoport tulajdonságainak beszereznie |
> | Microsoft.Compute/virtualMachines/*/read | Egy virtuális gép tulajdonságainak olvasása (virtuális gép méretei, futásidejű állapot, virtuálisgép-bővítmények stb.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet, és felszabadítja a számítási erőforrásokat |
> | Microsoft.Compute/virtualMachines/read | Virtuális gép tulajdonságainak beszereznie |
> | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Microsoft.DevTestLab/*/olvasás | Labor tulajdonságainak olvasása |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Igényeljen egy véletlenszerűen igényelhető virtuális gépet a laborban. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Hozzon létre virtuális gépeket egy laborban. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Győződjön meg arról, hogy az aktuális felhasználó rendelkezik egy érvényes profilt a laborban. |
> | Microsoft.DevTestLab/labs/formula/delete | Képletek törlése. |
> | Microsoft.DevTestLab/labs/formula/read | Képletek olvasása. |
> | Microsoft.DevTestLab/labs/formula/write | Képletek hozzáadása vagy módosítása |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Kiértékeli a laborházirendet. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Felsorolja a vonatkozó kezdési/leállítási ütemezéseket, ha vannak ilyenek. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Lekéri a virtuális gép RDP-fájljának tartalmát jelölő karakterláncot |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Egy terheléselosztó háttérkészletéhez csatlakozik. Nem riasztható. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Egy bejövő nat-szabály hoz egy terheléselosztót. Nem riasztható. |
> | Microsoft.Network/networkInterfaces/*/read | Olvassa el a hálózati adapter tulajdonságait (például az összes olyan terheléselosztót, amelynek a hálózati csatoló része) |
> | Microsoft.Network/networkInterfaces/join/action | Virtuális gép csatlakoztatása hálózati adapterhez. Nem riasztható. |
> | Microsoft.Network/networkInterfaces/read | Hálózati kapcsolat definíciója.  |
> | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy egy meglévő hálózati illesztőt frissít.  |
> | Microsoft.Network/publicIPAddresses/*/read | Nyilvános IP-cím tulajdonságainak olvasása |
> | Microsoft.Network/publicIPAddresses/join/action | Nyilvános IP-címhez csatlakozik. Nem riasztható. |
> | Microsoft.Network/publicIPAddresses/read | Nyilvános IP-cím definíciót kap. |
> | Microsoft.Network/virtualNetworks/alhálózatok/join/action | Virtuális hálózathoz csatlakozik. Nem riasztható. |
> | Microsoft.Resources/deployments/operations/read | Leésezése vagy listázása a telepítési műveleteket. |
> | Microsoft.Resources/deployments/read | Lekérődése vagy listázása a központi telepítések. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/listKeys/action | A megadott tárfiók hozzáférési kulcsait adja vissza. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Felsorolja azokat a méreteket, amelyekre a virtuális gép frissíthető |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="lab-creator"></a>Labor készítője

Lehetővé teszi a felügyelt laborok létrehozását, kezelését és törlését az Azure Lab-fiókok alatt.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Hozzon létre egy tesztkörnyezetet egy tesztkörnyezet-fiókban. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | A laborfiókban konfigurált egyes méretkategóriák területi elérhetőségi adatainak beszereznie |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | A tesztkörnyezet-fiók méret- és földrajzi adottságai kombinációinak díjszabása és elérhetősége. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action | Az előfizetés alapvető korlátozásainak és használatának beszereznie |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="monitor"></a>Figyelés


### <a name="application-insights-component-contributor"></a>Az Application Insights összetevőközreműködője

Az Application Insights-összetevők kezelése

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Klasszikus riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/metricAlerts/* | Új riasztási szabályok létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az Insights-összetevők létrehozása és kezelése |
> | Microsoft.Insights/webtests/* | Az Insights webes tesztjeinek létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="application-insights-snapshot-debugger"></a>Az Application Insights pillanatkép-hibakereső

A felhasználó engedélyt ad az Application Insights Snapshot Debugger által gyűjtött hibakeresési pillanatképek megtekintésére és letöltésére. Vegye figyelembe, hogy ezek az engedélyek nem szerepelnek a [Tulajdonos](#owner) vagy [a Közreműködő](#contributor) szerepkörben.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="monitoring-contributor"></a>Közreműködő figyelése

Képes olvasni az összes figyelési adatokat, és szerkesztheti monitoring beállításokat. Lásd [még: A szerepkörökkel, engedélyekkel és biztonsággal kapcsolatos első lépések az Azure Monitor segítségével.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsÖsszefoglaló/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Insights/components/* | Az Insights-összetevők létrehozása és kezelése |
> | Microsoft.Insights/DiagnosticSettings/* | Az Analysis Server diagnosztikai beállításának létrehozása, frissítése vagy beolvasása |
> | Microsoft.Insights/eventtypes/* | Tevékenységnapló-események (felügyeleti események) listázása egy előfizetésben. Ez az engedély a tevékenységnaplóhoz való programozott és portálos hozzáférésre is vonatkozik. |
> | Microsoft.Insights/LogDefinitions/* | Ez az engedély szükséges azoknak a felhasználóknak, akiknek a portálon keresztül hozzáférésre van szükségük a tevékenységnaplókhoz. A naplókategóriák listázása a Tevékenységnaplóban. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Metrikadefiníciók olvasása (egy erőforrás elérhető metrikatípusainak listája). |
> | Microsoft.Insights/Metrics/* | Egy erőforrás metrikáinak olvasása. |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Az Insights webes tesztjeinek létrehozása és kezelése |
> | Microsoft.Insights/munkafüzetek/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Írás/írás/törlés log analytics megoldás csomagok. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Olvasási/írási/törlési naplóelemzési mentett keresések. |
> | Microsoft.OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft.OperationalInsights/munkaterületek/megosztottkulcsok/művelet | A munkaterület megosztott kulcsainak beolvasása. Ezek a kulcsok a Microsoft Operational Insights-ügynökök és a munkaterület összekapcsolására szolgálnak. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Olvasási/írási/törlési naplóelemzési tárolási betekintési konfigurációk. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.WorkloadMonitor/monitorok/* |  |
> | Microsoft.WorkloadMonitor/notificationBeállítások/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
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

### <a name="monitoring-metrics-publisher"></a>Monitormetrika közzétevő

Engedélyezi a metrikák közzétételét az Azure-erőforrásokkal szemben

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="monitoring-reader"></a>Monitoring olvasó

Képes olvasni az összes figyelési adatok (metrikák, naplók, stb.) Lásd [még: A szerepkörökkel, engedélyekkel és biztonsággal kapcsolatos első lépések az Azure Monitor segítségével.](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.OperationalInsights/munkaterületek/keresés/művelet | Keresési lekérdezés végrehajtása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="workbook-contributor"></a>Munkafüzet közreműködője

Mentheti a közös munkafüzeteket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Insights/munkafüzetek/írás | Munkafüzet létrehozása vagy frissítése |
> | Microsoft.Insights/munkafüzetek/törlés | Munkafüzet törlése |
> | Microsoft.Insights/munkafüzetek/olvasás | Munkafüzet olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Képes olvasni a munkafüzeteket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | microsoft.insights/munkafüzetek/olvasás | Munkafüzet olvasása |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="management--governance"></a>Menedzsment + irányítás


### <a name="automation-job-operator"></a>Automatizálási feladat operátor

Feladatok létrehozása és kezelése automatizálási runbookokkal.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook-feldolgozói erőforrások olvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation-feladat beszerzése |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Folytatja az Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállít egy Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Beszerzi az Azure Automation feladatfolyamát |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Leveszi egy feladat kimenetét |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Az Automatizálási operátorok elindíthatják, leállíthatják, felfüggeszthetik és folytathatják a feladatokat

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook-feldolgozói erőforrások olvasása |
> | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation-feladat beszerzése |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Folytatja az Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállít egy Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Beszerzi az Azure Automation feladatfolyamát |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation-feladat felfüggesztése |
> | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation-feladatot |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Bekési egy Azure Automation-feladatütemezést |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation-feladatütemezést |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Az automatizálási fiókhoz kapcsolódó munkaterület beszerzése |
> | Microsoft.Automation/automationAccounts/read | Bekésel egy Azure Automation-fiókot |
> | Microsoft.Automation/automationAccounts/runbooks/read | Beszerzi az Azure Automation runbookot |
> | Microsoft.Automation/automationAccounts/schedules/read | Beszerzésegy Azure Automation-ütemezési eszköz |
> | Microsoft.Automation/automationAccounts/schedules/write | Azure Automation-ütemezési eszköz létrehozása vagy frissítése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Leveszi egy feladat kimenetét |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="automation-runbook-operator"></a>Automatizálási runbook-operátor

Olvassa el a Runbook-tulajdonságokat – a runbook-feladatok létrehozásához.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Automation/automationAccounts/runbooks/read | Beszerzi az Azure Automation runbookot |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-connected-machine-onboarding"></a>Azure connected machine bevezetés

Az Azure Connected Machines-t belehet kapcsolni.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.HybridCompute/machines/read | Bármilyen Azure Arc-gép olvasása |
> | Microsoft.HybridCompute/machines/write | Azure Arc-gépek írása |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Vendégkonfiguráció-hozzárendelés beszereznie. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="azure-connected-machine-resource-administrator"></a>Azure Connected Machine erőforrás-rendszergazda

Olvashat, írhat, törölhet és újra felveheti az Azure Connected Machines gépeket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.HybridCompute/machines/read | Bármilyen Azure Arc-gép olvasása |
> | Microsoft.HybridCompute/machines/write | Azure Arc-gépek írása |
> | Microsoft.HybridCompute/machines/delete | Azure Arc-gépek törlése |
> | Microsoft.HybridCompute/machines/reconnect/action | Újracsatlakoztat egy Azure Arc-gépet |
> | Microsoft.HybridCompute/machines/extensions/write | Azure Arc-bővítmények telepítése vagy frissítése |
> | Microsoft.HybridCompute/*/read |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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
        "Microsoft.HybridCompute/machines/extensions/write",
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

### <a name="billing-reader"></a>Számlázás olvasója

Lehetővé teszi az olvasási hozzáférést a számlázási adatokhoz

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Billing/*/olvasás | Számlázási adatok olvasása |
> | Microsoft.Commerce/*/olvasás |  |
> | Microsoft.Fogyasztás/*/olvasás |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | Microsoft.CostManagement/*/olvasás |  |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="blueprint-contributor"></a>Blueprint közreműködő

Kezelheti a tervezetdefiníciókat, de nem rendelheti hozzá őket.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Blueprint/blueprints/* | Tervrajzok-definíciók vagy tervrajzok összetevőinek létrehozása és kezelése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="blueprint-operator"></a>Terv operátor

Meglévő közzétett tervrajzok hozzárendelése, de nem hozhat létre új tervrajzokat. Vegye figyelembe, hogy ez csak akkor működik, ha a hozzárendelés egy felhasználó által hozzárendelt felügyelt identitással történik.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Blueprint/blueprintAssignments/* | Tervhozzárendelések létrehozása és kezelése. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="cost-management-contributor"></a>Költségkezelés közreműködője

Megtekintheti a költségeket és kezelheti a költségkonfigurációt (pl. költségvetések, export)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Fogyasztás/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingIdőszakok/olvasás |  |
> | Microsoft.Resources/subscriptions/read | Leveszi az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beszereznie |
> | Microsoft.Advisor/recommendations/read | Javaslatok olvasása |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="cost-management-reader"></a>Költségkezelési olvasó

Megtekintheti a költségadatokat és a konfigurációt (pl. költségvetések, exportok)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Fogyasztás/*/olvasás |  |
> | Microsoft.CostManagement/*/olvasás |  |
> | Microsoft.Billing/billingIdőszakok/olvasás |  |
> | Microsoft.Resources/subscriptions/read | Leveszi az előfizetések listáját. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Advisor/configurations/read | Konfigurációk beszereznie |
> | Microsoft.Advisor/recommendations/read | Javaslatok olvasása |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="managed-application-contributor-role"></a>Felügyelt alkalmazásközreműködői szerepkör

Lehetővé teszi a felügyelt alkalmazás-erőforrások létrehozását.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Solutions/applications/* |  |
> | Microsoft.Solutions/register/action | Regisztráljon a Megoldások ba. |
> | Microsoft.Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="managed-application-operator-role"></a>Felügyelt alkalmazásfelelős szerepkör

Lehetővé teszi a felügyelt alkalmazás-erőforrások olvasását és műveletek elvégzését

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Solutions/applications/read | Beolvassa az alkalmazások listáját. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Lehetővé teszi az erőforrások olvasását egy felügyelt alkalmazásban, és jit-hozzáférés kérése.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

A felügyelt szolgáltatások regisztrációs hozzárendelésének törlése szerepkör lehetővé teszi, hogy a kezelő bérlői felhasználók töröljék a bérlőhöz rendelt regisztrációs hozzárendelést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.ManagedServices/registrationAssignments/read | A felügyelt szolgáltatások regisztrációs hozzárendeléseinek listájának beolvasása. |
> | Microsoft.ManagedServices/registrationAssignments/delete | Eltávolítja a felügyelt szolgáltatások regisztrációs hozzárendelését. |
> | Microsoft.ManagedServices/operationStatuses/read | Beolvassa az erőforrás működési állapotát. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Felügyeleti csoport közreműködői szerepkör

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | Microsoft.Management/managementGroups/subscriptions/delete | De-associates előfizetés a felügyeleti csoportból. |
> | Microsoft.Management/managementGroups/subscriptions/write | Meglévő előfizetés társítani a felügyeleti csoport. |
> | Microsoft.Management/managementGroups/write | Felügyeleti csoport létrehozása vagy frissítése. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Felügyeleti csoport olvasói szerepköre

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Management/managementGroups/read | A hitelesített felhasználó felügyeleti csoportjainak listázása. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="new-relic-apm-account-contributor"></a>Új Relic APM-fiók közreműködője

Lehetővé teszi az új relic alkalmazásteljesítmény-kezelési fiókok és alkalmazások kezelését, de a hozzájuk való hozzáférést nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | NewRelic.APM/számlák/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="policy-insights-data-writer-preview"></a>Házirend-elemzési adatok megírója (előzetes verzió)

Lehetővé teszi az olvasási hozzáférést az erőforrás-házirendekhez és az írási hozzáférést az erőforrás-összetevők házirendeseményeihez.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/policyassignments/read | Információ a házirend-hozzárendelésekről. |
> | Microsoft.Authorization/policydefinitions/read | Információ a házirend-definícióról. |
> | Microsoft.Authorization/policysetdefinitions/read | Információ a házirendkészlet-definícióról. |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Ellenőrizze egy adott összetevő megfelelőségi állapotát az adatházirendekkel szemben. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Naplózza az erőforrás-összetevő házirend-eseményeit. |
> | **NotDataActions** |  |
> | *nincs* |  |

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

Az erőforrás-házirend létrehozására/módosítására, támogatási jegy létrehozására és az erőforrások/hierarchia olvasására jogosult felhasználók.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | */olvasás | A titkok kivételével minden típusú erőforrást elkell olvasni. |
> | Microsoft.Authorization/policyassignments/* | Házirend-hozzárendelések létrehozása és kezelése |
> | Microsoft.Authorization/policydefinitions/* | Házirend-definíciók létrehozása és kezelése |
> | Microsoft.Authorization/policysetdefinitions/* | Házirendkészletek létrehozása és kezelése |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="site-recovery-contributor"></a>Webhely-helyreállítási közreműködő

Lehetővé teszi a Site Recovery szolgáltatás kezelését, kivéve a tároló létrehozását és a szerepkör-hozzárendelést

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Az allocatestamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Az Erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás-/tároló hitelesítő tanúsítványát. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | A tárolóval kapcsolatos bővített adatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Regisztrált identitások létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Replikációs riasztási beállítások létrehozása vagy frissítése |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármilyen esemény elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Replikációs hálók létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Replikációs házirendek létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Helyreállítási tervek létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | A Recovery Services-tároló tárolási konfigurációjának létrehozása és kezelése |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Vault Token művelet vault token beszerezhető a tároló szintű háttérműveletek. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Helyreállítási szolgáltatások tárolójának riasztásai olvasása |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | A tároló replikációs műveletállapotának olvasása |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="site-recovery-operator"></a>Hely-helyreállítási operátor

Lehetővé teszi a feladatátvételt és a feladatátvételt, de nem hajthatja végre a site recovery felügyeleti műveleteit

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beszereznie |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Az allocatestamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Kiterjesztett információ beszerezése művelet leveszi egy objektum kiterjesztett adatait, amely az Azure-erőforrástípusát ?vault? |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelet használható a művelet állapotának és az aszinkron módon elküldött művelet eredményének lekérnie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A Get Containers művelet használható lekérni egy erőforrás regisztrált tárolók. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | A riasztások beállításainak olvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármilyen esemény elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | A szövet konzisztenciájának ellenőrzése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Olvassa el a szövetek |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újratársítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Tanúsítvány megújítása a hálóhoz |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármely hálózat olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Bármilyen védelmi tároló olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elem újravédelme |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Kapcsolóvédelmi tároló |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Feladatátvétel tesztelése karbantartás |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Védelmi tárolóleképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | A helyreállítási szolgáltatások szolgáltatóinak elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Bármilyen tárolási besorolás olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Storage-besorolás-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Replikációs feladatok létrehozása és kezelése |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el a szabályzatokat |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvételi véglegesítési helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvételi helyreállítási terv |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Helyreállítási terv újbóli védelme |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi helyreállítási terv tesztelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Feladatátvételi karbantartás helyreállítási tervének tesztelése |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvételi helyreállítási terv |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | A Helyreállítási szolgáltatások tárolójának riasztásai olvasása |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Vault Token művelet vault token beszerezhető a tároló szintű háttérműveletek. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Storage/storageAccounts/read | Visszaadja a tárfiókok listáját, vagy lekéri a megadott tárfiók tulajdonságait. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="site-recovery-reader"></a>Webhely-helyreállítási olvasó

Lehetővé teszi a Webhely-helyreállítási állapot megtekintését, de más felügyeleti műveletek elvégzését nem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | A GetAllocatedStamp a szolgáltatás által használt belső művelet |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Kiterjesztett információ beszerezése művelet leveszi egy objektum kiterjesztett adatait, amely az Azure-erőforrástípusát ?vault? |
> | Microsoft.RecoveryServices/Vaults/monitoringRiasztások/olvasás | Lekéri a helyreállítási szolgáltatások tárolójának riasztásait. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | A Get Vault művelet lead egy "vault" típusú Azure-erőforrást képviselő objektumot |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A Get Operation Results művelet használható a művelet állapotának és az aszinkron módon elküldött művelet eredményének lekérnie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A Get Containers művelet használható lekérni egy erőforrás regisztrált tárolók. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | A riasztások beállításainak olvasása |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Bármilyen esemény elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Olvassa el a szövetek |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármely hálózat olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Bármilyen védelmi tároló olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Védelmi tárolóleképezések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | A helyreállítási szolgáltatások szolgáltatóinak elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Bármilyen tárolási besorolás olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Storage-besorolás-hozzárendelések olvasása |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter elolvasása |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Bármilyen állás olvasása |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Olvassa el a szabályzatokat |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek elolvasása |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | A Recovery Services-trezor használati adatait adja vissza. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A Vault Token művelet vault token beszerezhető a tároló szintű háttérműveletek. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="support-request-contributor"></a>Támogatási kérelem közreműködője

Lehetővé teszi támogatási kérelmek létrehozását és kezelését

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="tag-contributor"></a>Címke közreműködő

Lehetővé teszi az entitások címkéinek kezelését anélkül, hogy magukhoz az entitásokhoz biztosítana hozzáférést.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Lekéri az erőforráscsoport erőforrásait. |
> | Microsoft.Resources/subscriptions/resources/read | Egy előfizetés erőforrásainak bekerülése. |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | Microsoft.Resources/tags/* |  |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Egyéb


### <a name="biztalk-contributor"></a>BizTalk közreműködő

Lehetővé teszi a BizTalk szolgáltatások kezelését, de a hozzáférésüket nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.BizTalkServices/BizTalk/* | BizTalk szolgáltatások létrehozása és kezelése |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

### <a name="scheduler-job-collections-contributor"></a>Ütemező feladatgyűjtemények közreműködője

Lehetővé teszi az Ütemező feladatgyűjtemények kezelését, de azok elérését nem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Műveletek** |  |
> | Microsoft.Authorization/*/olvasás | Szerepkörök és szerepkör-hozzárendelések olvasása |
> | Microsoft.Insights/alertRules/* | Az Insights riasztási szabályainak létrehozása és kezelése |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Lekéri a megadott hatókör összes erőforrásának rendelkezésre állási állapotát |
> | Microsoft.Resources/deployments/* | Erőforráscsoport-telepítések létrehozása és kezelése |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Erőforráscsoportok leése vagy listázása. |
> | Microsoft.Scheduler/jobcollections/* | Feladatgyűjtemények létrehozása és kezelése |
> | Microsoft.Támogatás/* | Támogatási jegyek létrehozása és kezelése |
> | **NotActions** |  |
> | *nincs* |  |
> | **DataActions (Adatműveletek)** |  |
> | *nincs* |  |
> | **NotDataActions** |  |
> | *nincs* |  |

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

## <a name="next-steps"></a>További lépések

- [Erőforrás-szolgáltató egyeztetése a szolgáltatással](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Egyéni Azure-szerepkörök](custom-roles.md)
- [Engedélyek az Azure Security Centerben](../security-center/security-center-permissions.md)
