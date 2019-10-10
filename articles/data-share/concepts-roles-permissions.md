---
title: Az Azure-beli adatmegosztás előzetes verziójának szerepkörei és követelményei
description: Ismerje meg az adatszolgáltatók és az adatfogyasztók hozzáférés-vezérlési szerepköreinek és követelményeinek megadását az Azure-beli adatmegosztás előzetes verziójában.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: c0841f6386440776c6ea719f9932a53cada9d9c4
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166377"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Az Azure-beli adatmegosztás előzetes verziójának szerepkörei és követelményei

Ez a cikk azokat a szerepköröket ismerteti, amelyek szükségesek az Azure-beli adatmegosztás előzetes verziójának használatával történő adatmegosztáshoz, valamint az Azure-beli adatmegosztás előzetes verziójával történő adatfogadáshoz. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Az Azure-adatmegosztás felügyelt identitásokat használ az Azure-szolgáltatásokhoz (korábbi nevén rendszercsomagok) az alapul szolgáló Storage-fiókok hitelesítéséhez, hogy képes legyen beolvasni az adatszolgáltató által megosztott adatokat, valamint az adatfogyasztóként megosztott adatokat. Ennek eredményeképpen az adatszolgáltató és az adatfogyasztó közötti hitelesítő adatok cseréje nem történik meg. 

A Managed Service Identity hozzáférést kell biztosítani a mögöttes Storage-fiókokhoz. Az Azure adatmegosztási szolgáltatás az Azure-beli adatmegosztási erőforrás Managed Service Identity használja az adatolvasásra és-írásra. Az Azure-beli adatmegosztások felhasználójának képesnek kell lennie szerepkör-hozzárendelés létrehozására a Managed Service Identity ahhoz a Storage-fiókhoz, amelyben az adatok megoszthatók a vagy a rendszerből. A szerepkör-hozzárendelések létrehozásához szükséges engedély a **tulajdonosi** szerepkörben, a felhasználói hozzáférés rendszergazdai szerepkörben, vagy a Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély hozzárendelésével rendelkező egyéni szerepkörben található. 

Ha nem tulajdonosa a szóban forgó Storage-fióknak, és nem tud szerepkör-hozzárendelést létrehozni az Azure-beli adatmegosztási erőforrás felügyelt identitásához, kérheti az Azure-rendszergazdát, hogy hozzon létre egy szerepkör-hozzárendelést az Ön nevében. 

Alább látható az adatmegosztási erőforrás által felügyelt identitáshoz rendelt szerepkörök összefoglalása:

| |  |  |
|---|---|---|
|**Tárolási típus**|**Adatszolgáltató forrás Storage-fiókja**|**Adatfogyasztói célként szolgáló Storage-fiók**|
|Azure Blob Storage| Storage blob-Adatolvasó | Storage blob adatközreműködői
|Azure Data Lake Gen1 | Tulajdonos | Nem támogatott
|Azure Data Lake Gen2 | Storage blob-Adatolvasó | Storage blob adatközreműködői
|
### <a name="data-providers"></a>Adatszolgáltatók 
Az adatkészletek Azure-adatmegosztáshoz való hozzáadásához az adatszolgáltatók adatmegosztási erőforrás-felügyelt identitását hozzá kell adni a Storage blob Adatolvasó szerepkörhöz. Ezt az Azure-adatmegosztási szolgáltatás automatikusan elvégzi, ha a felhasználó az Azure-on keresztül ad hozzá adatkészleteket, és a Storage-fiók tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amely rendelkezik a Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély hozzárendelésével. 

Azt is megteheti, hogy a felhasználó egy Azure-rendszergazda hozzáadhatja az adatmegosztási erőforrás által felügyelt identitást a Storage blob adatolvasói szerepkörhöz manuálisan. A szerepkör-hozzárendelésnek a rendszergazda általi manuális létrehozása érvénytelenné válik a Storage-fiók tulajdonosaként, vagy egyéni szerepkör-hozzárendeléssel. Ez az Azure Storage-ból vagy Azure Data Lake Gen2 megosztott adatokra vonatkozik. 

Azure Data Lake Gen1 származó adatok megosztásakor a szerepkör-hozzárendelést a tulajdonos szerepkörhöz kell beírni. 

Az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelés létrehozásához kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *szerepkör*területen válassza a *Storage blob-Adatolvasó*lehetőséget.
1. A *kiválasztás*mezőben adja meg az Azure-beli adatmegosztási fiók nevét.
1. Kattintson a *Save* (Mentés) gombra.

### <a name="data-consumers"></a>Adatfogyasztók
Az adatok fogadásához hozzá kell adni az adatfogyasztók adatmegosztását erőforrás által felügyelt identitást a Storage blob adatközreműködői szerepkörhöz. Ez a szerepkör szükséges ahhoz, hogy az Azure-beli adatmegosztási szolgáltatás képes legyen írni a Storage-fiókba. Ezt az Azure-adatmegosztási szolgáltatás automatikusan elvégzi, ha a felhasználó az Azure-on keresztül ad hozzá adatkészleteket, és a Storage-fiók tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amelynek van hozzárendelve a Microsoft. Authorization/szerepkör-hozzárendelés/írási engedély. 

Azt is megteheti, hogy a felhasználó egy Azure-rendszergazda hozzáadhatja az adatmegosztási erőforrás által felügyelt identitást a Storage blob adatközreműködői szerepkörhöz manuálisan. A szerepkör-hozzárendelésnek a rendszergazda általi manuális létrehozása érvénytelenné válik a Storage-fiók tulajdonosaként, vagy egyéni szerepkör-hozzárendeléssel. Vegye figyelembe, hogy ez az Azure Storage-ban vagy Azure Data Lake Gen2 megosztott adatvédelemre vonatkozik. Az Adatfogadás Azure Data Lake Gen1 nem támogatott. 

Ha manuálisan szeretné létrehozni az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelést, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *szerepkör*területen válassza a *Storage blob-adatközreműködő*elemet. 
1. A *kiválasztás*mezőben adja meg az Azure-beli adatmegosztási fiók nevét.
1. Kattintson a *Save* (Mentés) gombra.

Ha REST API-kkal oszt meg adatmegosztást, ezeket a szerepkör-hozzárendeléseket manuálisan kell létrehoznia az adatmegosztási fióknak a megfelelő szerepkörökbe való hozzáadásával. 

A szerepkör-hozzárendelés hozzáadásával kapcsolatos további tudnivalókért tekintse meg [ezt a dokumentációt,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) amely azt ismerteti, hogyan adhat hozzá szerepkör-hozzárendelést egy Azure-erőforráshoz. 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltató regisztrálása 

Az Azure-beli adatmegosztási meghívások elfogadásakor manuálisan kell regisztrálnia a Microsoft. DataShare erőforrás-szolgáltatót az előfizetésében. Az alábbi lépéseket követve regisztrálja a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében. 

1. A Azure Portal navigáljon az **előfizetések**elemre.
1. Válassza ki az Azure-adatmegosztáshoz használt előfizetést.
1. Kattintson az **erőforrás-szolgáltatók**elemre.
1. Keressen rá a Microsoft. DataShare kifejezésre.
1. Kattintson a **regisztrálás**gombra.

## <a name="next-steps"></a>Következő lépések

- További információ az Azure-beli szerepkörökről – a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

