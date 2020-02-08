---
title: Az Azure Data Share szerepkörei és követelményei
description: Ismerje meg az Azure-adatmegosztás használatával történő adatmegosztáshoz és az adatfogadáshoz szükséges engedélyeket.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087172"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Az Azure Data Share szerepkörei és követelményei 

Ez a cikk azokat a szerepköröket és engedélyeket ismerteti, amelyek szükségesek az Azure adatmegosztási szolgáltatással történő adatmegosztáshoz és fogadáshoz. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Az Azure adatmegosztási szolgáltatással az adatszolgáltató és a fogyasztó közötti hitelesítő adatok cseréje nélkül is megoszthatja az adatokat. Az Azure adatmegosztási szolgáltatás felügyelt identitásokat (korábbi nevén rendszercsomagok) használ az Azure-adattárba való hitelesítéshez. 

Az Azure-beli adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítani az Azure-adattárhoz. Az Azure adatmegosztási szolgáltatás ezután ezt a felügyelt identitást használja az adatok olvasására és írására a pillanatkép-alapú megosztáshoz, valamint a szimbolikus hivatkozás létrehozásához a helyi megosztáshoz. 

Az Azure-adattárból származó adatok megosztásához vagy fogadásához a felhasználónak legalább a következő engedélyekkel kell rendelkeznie. Az SQL-alapú megosztáshoz további engedélyek szükségesek.
* Engedély az Azure-adattárba való íráshoz. Ez az engedély általában a **közreműködő** szerepkörben található.
* Engedély a szerepkör-hozzárendelés létrehozásához az Azure-adattárban. A szerepkör-hozzárendelések létrehozásához általában a **tulajdonosi** szerepkör, a felhasználói hozzáférés rendszergazdai szerepköre, vagy a Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély hozzárendelésére vonatkozó engedély van rendelve. Ez az engedély nem szükséges, ha az adatmegosztási erőforrás felügyelt identitása már hozzáférést kap az Azure-adattárhoz. Az alábbi táblázatban találja a szükséges szerepkört.

Az alábbiakban az adatmegosztási erőforrás felügyelt identitásához rendelt szerepkörök összegzése látható:

| |  |  |
|---|---|---|
|**Adattár típusa**|**Adatszolgáltató forrásának adattára**|**Adatfogyasztói célként szolgáló adattár**|
|Azure Blob Storage| Storage blob-Adatolvasó | Storage blob adatközreműködői
|Azure Data Lake Gen1 | Tulajdonos | Nem támogatott
|Azure Data Lake Gen2 | Storage blob-Adatolvasó | Storage blob adatközreműködői
|Azure-SQL Server | SQL-adatbázis közreműködői | SQL-adatbázis közreműködői
|Azure Adatkezelő-fürt | Közreműködő | Közreműködő
|

SQL-alapú megosztás esetén az SQL-felhasználót az Azure-beli adatmegosztási erőforrással megegyező nevű külső szolgáltatóból kell létrehozni az SQL Database-ben. Az alábbiakban az SQL-felhasználó által megkövetelt engedélyek összegzése látható.

| |  |  |
|---|---|---|
|**SQL Database típusa**|**Adatszolgáltató SQL-felhasználói engedélye**|**Adatfogyasztói SQL-felhasználói engedély**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (korábban SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Adatszolgáltató 
Az adatkészletek Azure-adatmegosztásban való hozzáadásához a szolgáltatói adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítania a forrás Azure-adattárhoz. A Storage-fiók esetében például az adatmegosztási erőforrás felügyelt identitása megkapja a Storage blob adatolvasói szerepkört. 

Ezt az Azure-adatmegosztási szolgáltatás automatikusan elvégzi, ha a felhasználó Azure Portalon keresztül adja hozzá az adatkészletet, és a felhasználó rendelkezik a megfelelő engedélyekkel. Például a felhasználó az Azure-beli adattár tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amely rendelkezik a Microsoft. Authorization/szerepkör-hozzárendelésekkel/írási engedéllyel. 

Azt is megteheti, hogy a felhasználó az Azure adattár tulajdonosaként manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárban. Ezt a műveletet csak egyszer kell végrehajtani az adatmegosztási erőforráson.

Az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelés létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon az Azure-adattárhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *szerepkör*területen válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például Storage-fiók esetén válassza a *Storage blob-Adatolvasó*lehetőséget).
1. A *kiválasztás*mezőben adja meg az Azure-beli adatmegosztási erőforrás nevét.
1. Kattintson a *Save* (Mentés) gombra.

SQL-alapú források esetében a fenti lépéseken kívül egy SQL-felhasználót is létre kell hozni az SQL Database-ben az Azure-beli adatmegosztási erőforrással megegyező névvel rendelkező külső szolgáltatótól. Ezt a felhasználót *db_datareader* jogosultsággal kell megadnia. Az SQL-alapú megosztás egyéb előfeltételeivel együtt egy minta parancsfájl is megtalálható az [adatmegosztási](share-your-data.md) oktatóanyagban. 

### <a name="data-consumer"></a>Adatfogyasztó
Az adatok fogadásához a fogyasztói adatmegosztási erőforrás felügyelt identitásának hozzáférést kell biztosítania a cél Azure-adattárhoz. A Storage-fiók esetében például az adatmegosztási erőforrás felügyelt identitása megkapja a Storage blob adatközreműködői szerepkört. 

Ezt az Azure-adatmegosztási szolgáltatás automatikusan végzi el, ha a felhasználó a Azure Portalon keresztül határozza meg a cél adattárat, és a felhasználó rendelkezik megfelelő engedéllyel. A felhasználó például az Azure adattár tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amelynek van hozzárendelve a Microsoft. Authorization/szerepkör-hozzárendelés/írás engedélye. 

Azt is megteheti, hogy a felhasználó az Azure adattár tulajdonosaként manuálisan hozzáadja az adatmegosztási erőforrás felügyelt identitását az Azure-adattárban. Ezt a műveletet csak egyszer kell végrehajtani az adatmegosztási erőforráson.

Ha manuálisan szeretné létrehozni az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelést, kövesse az alábbi lépéseket:

1. Navigáljon az Azure-adattárhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *szerepkör*területen válassza ki a szerepkört a fenti szerepkör-hozzárendelési táblában (például Storage-fiók esetén válassza a *Storage blob-Adatolvasó*lehetőséget).
1. A *kiválasztás*mezőben adja meg az Azure-beli adatmegosztási erőforrás nevét.
1. Kattintson a *Save* (Mentés) gombra.

SQL-alapú célként a fenti lépéseken kívül egy SQL-felhasználót is létre kell hozni az SQL Database-ben az Azure-beli adatmegosztási erőforrással megegyező névvel rendelkező külső szolgáltatótól. Ezt a felhasználót *db_datareader, db_datawriter, db_ddladmin* engedéllyel kell megadnia. A rendszer az SQL-alapú megosztás egyéb előfeltételeivel együtt egy minta parancsfájlt is megtalál az [elfogadás és fogadás adatkezelési](subscribe-to-data-share.md) oktatóanyagban. 

Ha REST API-k használatával oszt meg adatmegosztást, manuálisan kell létrehoznia ezeket a szerepkör-hozzárendeléseket. 

A szerepkör-hozzárendelés hozzáadásával kapcsolatos további tudnivalókért tekintse meg [ezt a dokumentációt](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltató regisztrálása 

Ha először szeretné megtekinteni az Azure-beli adatmegosztási meghívást az Azure-bérlőben, előfordulhat, hogy manuálisan kell regisztrálnia a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében. Az alábbi lépéseket követve regisztrálja a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében. Az erőforrás-szolgáltató regisztrálásához *közreműködői* hozzáférésre van szüksége az Azure-előfizetéshez.

1. A Azure Portal navigáljon az **előfizetések**elemre.
1. Válassza ki az Azure-adatmegosztáshoz használt előfizetést.
1. Kattintson az **erőforrás-szolgáltatók**elemre.
1. Keressen rá a Microsoft. DataShare kifejezésre.
1. Kattintson a **regisztrálás**gombra.

## <a name="next-steps"></a>Következő lépések

- További információ az Azure-beli szerepkörökről – a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

