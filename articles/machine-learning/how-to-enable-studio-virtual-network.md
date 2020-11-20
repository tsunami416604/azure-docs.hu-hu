---
title: Azure Machine Learning Studio engedélyezése virtuális hálózaton
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan konfigurálhatja Azure Machine Learning studiót a virtuális hálózatban tárolt adatmennyiség eléréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: df4d777ad78240b3ca84c51152b37861c4ccc486
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960002"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure Machine Learning Studio használata Azure-beli virtuális hálózaton

Ebből a cikkből megtudhatja, hogyan használhatja a Azure Machine Learning Studio alkalmazást egy virtuális hálózaton. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - A Studio egy virtuális hálózaton belüli erőforrásból érhető el.
> - Magánhálózati végpontok konfigurálása a Storage-fiókokhoz.
> - A Studio hozzáférést biztosít a virtuális hálózaton belül tárolt adatmennyiségekhez.
> - Ismerje meg, hogy a Studio hogyan befolyásolja a tárolók biztonságát.

Ez a cikk egy öt részes sorozat ötödik része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el az első [részt: VNet – áttekintés](how-to-network-security-overview.md) az általános architektúra megismeréséhez. 

Tekintse meg a sorozat egyéb cikkeit:

[1. VNet – áttekintés](how-to-network-security-overview.md)  >  [2. A](how-to-secure-workspace-vnet.md)  >  [3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)  >  **. A Studio funkcióinak engedélyezése**


> [!IMPORTANT]
> Ha a munkaterület __szuverén felhőben__ van, például Azure Government vagy az Azure China 21Vianet, az integrált jegyzetfüzetek _nem_ támogatják a virtuális hálózatban lévő tárolók használatát. Ehelyett használhatja egy számítási példány Jupyter-notebookjait. További információkért tekintse meg a [hozzáférési adatokat egy számítási példányú jegyzetfüzet](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) szakaszban.


## <a name="prerequisites"></a>Előfeltételek

+ A [hálózati biztonság áttekintése című témakörben](how-to-network-security-overview.md) megismerheti a gyakori virtuális hálózati forgatókönyveket és az általános virtuális hálózati architektúrát.

+ Egy korábban létező virtuális hálózat és alhálózat.

+ Meglévő [Azure Machine learning munkaterület, amelyen engedélyezve van a privát hivatkozás](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Egy meglévő [Azure Storage-fiók lett hozzáadva a virtuális hálózathoz](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>A studióhoz való hozzáférés a VNet belüli erőforrásból

Ha egy virtuális hálózaton (például egy számítási példányon vagy virtuális gépen) belül található erőforráshoz fér hozzá a studióhoz, engedélyeznie kell a kimenő forgalmat a virtuális hálózatról a studióhoz. 

Ha például hálózati biztonsági csoportokat (NSG) használ a kimenő forgalom korlátozására, adjon hozzá egy szabályt a __AzureFrontDoor. frontend__ nevű __szolgáltatási címkéhez__ .

## <a name="access-data-using-the-studio"></a>Hozzáférés az adataihoz a Studio használatával

Miután hozzáadta az Azure Storage-fiókot a virtuális hálózathoz egy [szolgáltatási végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) vagy [privát végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), konfigurálnia kell a Storage-fiókot úgy, hogy a [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) használja a Studio számára az adatokhoz való hozzáférés biztosításához.

Ha nem engedélyezi a felügyelt identitást, akkor ez a hibaüzenet jelenik meg, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` továbbá a következő műveletek lesznek letiltva:

* A Studióban tárolt előzetes verzió.
* Jelenítse meg a tervezőben tárolt adatmegjelenítést.
* AutoML-kísérlet küldése.
* Címkéző projekt elindítása.

A Studio a következő adattár-típusokból származó adatok olvasását támogatja egy virtuális hálózatban:

* Azure-blob
* 1. generációs Azure Data Lake Storage
* 2. generációs Azure Data Lake Storage
* Azure SQL Database

### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Munkaterület felügyelt identitás- __olvasó__ hozzáférésének engedélyezése a Storage privát hivatkozásához

Ez a lépés csak akkor szükséges, ha az Azure Storage-fiókot egy [privát végponttal](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)hozzáadta a virtuális hálózathoz. További információkért tekintse meg az [olvasó](../role-based-access-control/built-in-roles.md#reader) beépített szerepkörét.

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Adattárolók konfigurálása a munkaterület által felügyelt identitás használatára

Azure Machine Learning adattárolókat [használ a Storage-](concept-data.md#datastores) fiókokhoz való kapcsolódáshoz. A következő lépésekkel konfigurálhatja az adattárolókat a felügyelt identitás használatára. 

1. A Studióban __válassza az__ adattárolók lehetőséget.

1. Új adattár létrehozásához válassza az __+ új adattár__ lehetőséget.

    Meglévő adattár frissítéséhez válassza ki az adattárt, és válassza a __hitelesítő adatok frissítése__ lehetőséget.

1. Az adattár beállításainál válassza az __Igen__ lehetőséget a  __Azure Machine learning szolgáltatás számára a munkaterület által felügyelt identitás használatával való elérésének engedélyezéséhez__.


Ezek a lépések hozzáadják a munkaterület által felügyelt identitást __olvasóként__ a Storage szolgáltatáshoz az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával. Az __olvasói__ hozzáférés lehetővé teszi, hogy a munkaterület beolvassa a tűzfal beállításait, és gondoskodjon arról, hogy a virtuális hálózat ne maradjon meg

> [!NOTE]
> A módosítások érvénybe léptetése akár 10 percet is igénybe vehet.

## <a name="technical-notes-for-managed-identity"></a>Technikai megjegyzések a felügyelt identitáshoz

A felügyelt identitásnak a tárolási szolgáltatásokhoz való hozzáférése bizonyos biztonsági szempontokat is érint. Ez a szakasz az egyes Storage-fióktípus változásait ismerteti.

> [!IMPORTANT]
> Ezek a szempontok egyediek a hozzáférő __Storage-fiók típusától__ .

### <a name="azure-blob-storage"></a>Azure Blob Storage

Az __Azure Blob Storage__ esetében a munkaterület által felügyelt identitást is hozzáadja [blob-adatolvasóként](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , így az adatok a blob Storage-ból is olvashatók.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 hozzáférés-vezérlés

Az Azure RBAC és a POSIX stílusú hozzáférés-vezérlési listákat (ACL-eket) is használhatja a virtuális hálózaton belüli adatelérés szabályozására.

Az Azure RBAC használatához adja hozzá a munkaterület által felügyelt identitást a [blob-Adatolvasó](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) szerepkörhöz. További információ: [Azure szerepköralapú hozzáférés-vezérlés](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Az ACL-ek használatához a munkaterület által felügyelt identitás ugyanúgy rendelhető hozzá, mint bármely más biztonsági elv. További információ: hozzáférés- [vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 hozzáférés-vezérlés

Azure Data Lake Storage Gen1 csak a POSIX stílusú hozzáférés-vezérlési listát támogatja. A munkaterület által felügyelt identitás-hozzáférés az erőforrásokhoz ugyanúgy rendelhető hozzá, mint bármely más biztonsági elv. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database foglalt felhasználó

A felügyelt identitással Azure SQL Database tárolt adatok eléréséhez létre kell hoznia egy olyan SQL-beli felhasználót, amely a felügyelt identitásra van leképezve. Ha további információt szeretne arról, hogyan hozhat létre egy felhasználót egy külső szolgáltatótól, tekintse meg az [Azure ad-identitásokhoz leképezett felhasználók létrehozása](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)című témakört.

Miután létrehozta az SQL-T tartalmazó felhasználót, adja meg az engedélyt a [T-SQL parancs](/sql/t-sql/statements/grant-object-permissions-transact-sql)használatával.

### <a name="azure-machine-learning-designer-default-datastore"></a>Azure Machine Learning Designer alapértelmezett adattár

A tervező a munkaterülethez csatolt Storage-fiókot használja a kimenet alapértelmezett tárolásához. Megadhatja azonban azt is, hogy a kimenetet minden olyan adattárhoz tárolja, amelyhez hozzáfér. Ha a környezete virtuális hálózatokat használ, ezekkel a vezérlőelemekkel biztosíthatja, hogy az adatai biztonságosak és elérhetők maradjanak.

Új alapértelmezett tároló beállítása egy folyamathoz:

1. A folyamat piszkozata lapon válassza a **Beállítások fogaskerék ikont** a folyamat címe közelében.
1. Válassza az **alapértelmezett adattár kiválasztása** lehetőséget.
1. Új adattárt kell megadnia.

Az alapértelmezett adattárat is felülbírálhatja egy modul alapján. Ez lehetővé teszi az egyes modulok tárolási helyének szabályozását.

1. Válassza ki azt a modult, amelynek kimenetét meg kívánja adni.
1. Bontsa ki a **kimeneti beállítások** szakaszt.
1. Válassza az **alapértelmezett kimeneti beállítások felülbírálása** lehetőséget.
1. Válassza a **kimeneti beállítások megadása** lehetőséget.
1. Új adattárt kell megadnia.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy négy részből álló virtuális hálózati sorozat választható részét képezi. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [2. rész: a munkaterület erőforrásainak védelme](how-to-secure-workspace-vnet.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)