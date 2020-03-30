---
title: Az Azure Data Share szerepkörei és követelményei
description: Ismerje meg az Azure Data Share használatával az adatok megosztásához és fogadásához szükséges engedélyeket.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265505"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Az Azure Data Share szerepkörei és követelményei 

Ez a cikk az Azure Data Share szolgáltatás használatával az adatok megosztásához és fogadásához szükséges szerepköröket és engedélyeket ismerteti. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Az Azure Data Share szolgáltatással megoszthatja az adatokat anélkül, hogy hitelesítő adatokat cserélne az adatszolgáltató és a fogyasztó között. Az Azure Data Share szolgáltatás felügyelt identitások (korábbi nevén MSI-k) használatával hitelesíti magát az Azure-adattárban. 

Az Azure Data Share-erőforrás felügyelt identitásának hozzáférést kell biztosítani az Azure-adattárhoz. Az Azure Data Share szolgáltatás ezután ezt a felügyelt identitást használja az adatok olvasásához és írásához a pillanatkép-alapú megosztáshoz, valamint a szimbolikus hivatkozás létrehozásához a helyen történő megosztáshoz. 

Az Azure-adattólszármazó adatok megosztásához vagy fogadásához a felhasználónak legalább a következő engedélyekre van szüksége. Az SQL-alapú megosztáshoz további engedélyek szükségesek.
* Írási engedély az Azure-adattárba. Ez az engedély általában a **közreműködői** szerepkörben található.
* Az Azure-adattárban szerepkör-hozzárendelés létrehozására vonatkozó engedély. A szerepkör-hozzárendelések létrehozására általában a **Tulajdonos** szerepkörben, a Felhasználói hozzáférés rendszergazdájában vagy a Microsoft.Authorization/role assignments/write permission signed egyéni szerepkörben van engedély. Ez az engedély nem szükséges, ha az adatmegosztási erőforrás felügyelt identitása már hozzáférést kapott az Azure-adattárhoz. A szükséges szerepkört lásd az alábbi táblázatban.

Az alábbiakban az Adatmegosztási erőforrás felügyelt identitásához rendelt szerepkörök összegzése látható:

| |  |  |
|---|---|---|
|**Adattár típusa**|**Adatforrás-adatforrás-tár**|**Adatfogyasztói céladattár**|
|Azure Blob Storage| Tárolási blob adatolvasó | Storage Blob-adatközreműködő
|Azure Data Lake Gen1 | Tulajdonos | Nem támogatott
|Azure Data Lake Gen2 | Tárolási blob adatolvasó | Storage Blob-adatközreműködő
|Azure SQL Server | SQL DB közreműködő | SQL DB közreműködő
|Azure Data Explorer fürt | Közreműködő | Közreműködő
|

SQL-alapú megosztáshoz egy SQL-felhasználót kell létrehozni egy külső szolgáltató az SQL-adatbázisban az Azure Data Share erőforrás nevével. Az alábbiakban összefoglaljuk az SQL-felhasználó által kért engedélyt.

| |  |  |
|---|---|---|
|**SQL adatbázis típusa**|**Adatszolgáltató SQL felhasználói engedélye**|**Adatfogyasztó SQL-felhasználói engedélye**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (korábban SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Adatszolgáltató 
Ha hozzá szeretne adni egy adatkészletet az Azure Data Share-ben, a szolgáltatói adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítani a forrás Azure-adattárhoz. Például a tárfiók esetében az adatmegosztási erőforrás felügyelt identitása megkapja a Storage Blob Data Reader szerepkört. 

Ezt az Azure Data Share szolgáltatás automatikusan végzi, amikor a felhasználó adatkészletet ad hozzá az Azure Portalon keresztül, és a felhasználó rendelkezik a megfelelő engedéllyel. Például a felhasználó tulajdonosa az Azure-adattár, vagy tagja egy egyéni szerepkör, amely a Microsoft.Authorization/role hozzárendelések/írási engedély hozzárendelt. 

Azt is megteheti, hogy a felhasználó az Azure-adattár tulajdonosával manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárhoz. Ezt a műveletet csak egyszer kell végrehajtani egy adatmegosztási erőforrásonként.

Ha szerepkör-hozzárendelést szeretne létrehozni az adatmegosztási erőforrás felügyelt identitásához, kövesse az alábbi lépéseket:

1. Keresse meg az Azure-adattára.
1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**
1. Válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *Szerepkör csoportban*válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például a tárfiókesetében válassza a *Storage Blob Data Reader*lehetőséget).
1. A *Kijelölés mezőbe*írja be az Azure-adatmegosztási erőforrás nevét.
1. Kattintson a *Mentés* gombra.

SQL-alapú források, a fenti lépések mellett egy SQL-felhasználó kell létrehozni egy külső szolgáltató az SQL-adatbázis ban az Azure Data Share erőforrás neve. Ennek a felhasználónak *db_datareader* engedélyt kell kapnia. A mintaparancsfájl az SQL-alapú megosztás egyéb előfeltételeivel együtt az [adatmegosztási](share-your-data.md) oktatóanyagban található. 

### <a name="data-consumer"></a>Adatfogyasztó
Az adatok fogadásához a fogyasztói adatok megosztási erőforrás felügyelt identitásának hozzáférést kell biztosítani a cél Azure-adattárhoz. Például a tárfiók esetében az adatmegosztási erőforrás felügyelt identitása a Storage Blob Data Contributor szerepkört kapja. 

Ezt az Azure Data Share szolgáltatás automatikusan végzi el, ha a felhasználó megad egy céladat-tárolót az Azure Portalon keresztül, és a felhasználó megfelelő engedéllyel rendelkezik. Például a felhasználó tulajdonosa az Azure-adattár, vagy tagja egy egyéni szerepkör, amely a Microsoft.Authorization/role hozzárendelések/írási engedély hozzárendelt. 

Azt is megteheti, hogy a felhasználó az Azure-adattár tulajdonosával manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárhoz. Ezt a műveletet csak egyszer kell végrehajtani egy adatmegosztási erőforrásonként.

Ha manuálisan szeretne szerepkör-hozzárendelést létrehozni az adatmegosztási erőforrás felügyelt identitásához, kövesse az alábbi lépéseket:

1. Keresse meg az Azure-adattára.
1. Válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.**
1. Válassza **a Szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *Szerepkör csoportban*válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például a tárfiókesetében válassza a *Storage Blob Data Reader*lehetőséget).
1. A *Kijelölés mezőbe*írja be az Azure-adatmegosztási erőforrás nevét.
1. Kattintson a *Mentés* gombra.

SQL-alapú cél, a fenti lépések mellett egy SQL-felhasználó kell létrehozni egy külső szolgáltató az SQL-adatbázis ban az Azure Data Share erőforrás nevével. Ennek a felhasználónak *db_datareader, db_datawriter db_ddladmin* engedélyt kell adni. A mintaparancsfájl az SQL-alapú megosztás egyéb előfeltételeivel együtt az [adatok fogadása oktatóanyagban](subscribe-to-data-share.md) található. 

Ha rest API-k használatával oszt meg adatokat, ezeket a szerepkör-hozzárendeléseket manuálisan kell létrehoznia. 

Ha többet szeretne megtudni a szerepkör-hozzárendelés hozzáadásáról, olvassa el [ezt a dokumentációt.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltató regisztrációja 

Az Azure Data Share-meghívó első alkalommal az Azure-bérlőben való megtekintéséhez előfordulhat, hogy manuálisan kell regisztrálnia a Microsoft.DataShare erőforrás-szolgáltatót az Azure-előfizetésbe. Az alábbi lépésekkel regisztrálhatja a Microsoft.DataShare erőforrás-szolgáltatót az Azure-előfizetésébe. Az erőforrás-szolgáltató regisztrálásához *közreműködői* hozzáférésre van szüksége az Azure-előfizetéshez.

1. Az Azure Portalon keresse meg az Előfizetések című **ad.**
1. Válassza ki az Azure Data Share-hez használt előfizetést.
1. Kattintson az **Erőforrás-szolgáltatók gombra.**
1. Keresse meg a Microsoft.DataShare kifejezést.
1. Kattintson a **Regisztráció gombra.**

## <a name="next-steps"></a>További lépések

- További információ az Azure-beli szerepkörökről – [A szerepkör-definíciók megismerése](../role-based-access-control/role-definitions.md)

