---
title: Az Azure Data Share szerepkörei és követelményei
description: Ismerje meg az adatszolgáltatók és az adatfogyasztók hozzáférés-vezérlési szerepköreit és követelményeit az Azure-adatmegosztásban tárolt adatmegosztáshoz.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490579"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Az Azure Data Share szerepkörei és követelményei 

Ez a cikk azokat a szerepköröket ismerteti, amelyek szükségesek az Azure-adatmegosztás használatával történő adatmegosztáshoz, valamint az Azure-adatmegosztás használatával történő adatfogadáshoz és fogadáshoz. 

## <a name="roles-and-requirements"></a>Szerepkörök és követelmények

Az Azure-adatmegosztás felügyelt identitásokat használ az Azure-szolgáltatásokhoz (korábbi nevén rendszercsomagok) az alapul szolgáló Storage-fiókok hitelesítéséhez, hogy képes legyen beolvasni az adatszolgáltató által megosztott adatokat, valamint az adatfogyasztóként megosztott adatokat. Ennek eredményeképpen az adatszolgáltató és az adatfogyasztó közötti hitelesítő adatok cseréje nem történik meg. 

A Managed Service Identity hozzáférést kell biztosítani a mögöttes Storage-fiókhoz vagy az SQL-adatbázishoz. Az Azure adatmegosztási szolgáltatás az Azure-beli adatmegosztási erőforrás Managed Service Identity használja az adatolvasásra és-írásra. Az Azure-beli adatmegosztások felhasználójának képesnek kell lennie szerepkör-hozzárendelés létrehozására a Managed Service Identityhoz a Storage-fiókhoz vagy az SQL Database-hez, amelyről az adatok megoszthatók. 

A Storage esetében a szerepkör-hozzárendelések létrehozásához szükséges engedély a **tulajdonosi** szerepkörben, a felhasználói hozzáférés rendszergazdai szerepkörben, vagy a Microsoft. Authorization/szerepkör-hozzárendelések/írási engedéllyel rendelkező egyéni szerepkörben található. 

Ha nem tulajdonosa a szóban forgó Storage-fióknak, és nem tud szerepkör-hozzárendelést létrehozni az Azure-beli adatmegosztási erőforrás felügyelt identitásához, kérheti az Azure-rendszergazdát, hogy hozzon létre egy szerepkör-hozzárendelést az Ön nevében. 

Alább látható az adatmegosztási erőforrás által felügyelt identitáshoz rendelt szerepkörök összefoglalása:

| |  |  |
|---|---|---|
|**Tárolási típus**|**Adatszolgáltató-tároló**|**Adatfeldolgozó tároló**|
|Azure Blob Storage| Storage blob-Adatolvasó | Storage blob adatközreműködői
|Azure Data Lake Gen1 | Tulajdonos | Nem támogatott
|Azure Data Lake Gen2 | Storage blob-Adatolvasó | Storage blob adatközreműködői
|Azure SQL | dbo | dbo 
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

SQL-alapú források esetén a felhasználót az SQL-adatbázis egy külső szolgáltatójának kell létrehoznia, hogy az adatok ugyanazzal a névvel legyenek megosztva, mint az Azure-beli adatmegosztási fiókkal. Az SQL-alapú megosztás egyéb előfeltételeivel együtt egy minta parancsfájl is megtalálható az [adatmegosztási](share-your-data.md) oktatóanyagban. 

### <a name="data-consumers"></a>Adatfogyasztók
Az adatok fogadásához hozzá kell adni az adatfogyasztók adatmegosztását erőforrás által felügyelt identitást a Storage blob-adatközreműködői szerepkörhöz és/vagy egy SQL-adatbázis dbo-szerepköréhez, ha az adatok SQL-adatbázisba érkeznek. 

Tárolás esetén ezt az Azure adatmegosztási szolgáltatás automatikusan elvégzi, ha a felhasználó az Azure-on keresztül ad hozzá adatkészleteket, és a Storage-fiók tulajdonosa, vagy egy olyan egyéni szerepkör tagja, amely rendelkezik Microsoft. Authorization/szerepkör-hozzárendelésekkel/írási engedéllyel rendelt. 

Azt is megteheti, hogy a felhasználó egy Azure-rendszergazda hozzáadhatja az adatmegosztási erőforrás által felügyelt identitást a Storage blob adatközreműködői szerepkörhöz manuálisan. A szerepkör-hozzárendelésnek a rendszergazda általi manuális létrehozása érvénytelenné válik a Storage-fiók tulajdonosaként, vagy egyéni szerepkör-hozzárendeléssel. Vegye figyelembe, hogy ez az Azure Storage-ban vagy Azure Data Lake Gen2 megosztott adatvédelemre vonatkozik. Az Adatfogadás Azure Data Lake Gen1 nem támogatott. 

Ha manuálisan szeretné létrehozni az adatmegosztási erőforrás felügyelt identitásához tartozó szerepkör-hozzárendelést, kövesse az alábbi lépéseket:

1. Navigáljon a Storage-fiókhoz.
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása**lehetőséget.
1. A *szerepkör*területen válassza a *Storage blob-adatközreműködő*elemet. 
1. A *kiválasztás*mezőben adja meg az Azure-beli adatmegosztási fiók nevét.
1. Kattintson a *Save* (Mentés) gombra.

Ha REST API-kkal oszt meg adatmegosztást, ezeket a szerepkör-hozzárendeléseket manuálisan kell létrehoznia az adatmegosztási fióknak a megfelelő szerepkörökbe való hozzáadásával. 

Ha SQL-alapú forrásba fogadja az adatait, győződjön meg arról, hogy új felhasználó jön létre az Azure-beli adatmegosztási fiókkal megegyező nevű külső szolgáltatóról. Tekintse meg az előfeltételeket az [elfogadás és az adatfogadásra vonatkozó](subscribe-to-data-share.md) oktatóanyagban. 

A szerepkör-hozzárendelés hozzáadásával kapcsolatos további tudnivalókért tekintse meg [ezt a dokumentációt,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) amely azt ismerteti, hogyan adhat hozzá szerepkör-hozzárendelést egy Azure-erőforráshoz. 

## <a name="resource-provider-registration"></a>Erőforrás-szolgáltató regisztrálása 

Az Azure-beli adatmegosztási meghívások elfogadásakor manuálisan kell regisztrálnia a Microsoft. DataShare erőforrás-szolgáltatót az előfizetésében. Az alábbi lépéseket követve regisztrálja a Microsoft. DataShare erőforrás-szolgáltatót az Azure-előfizetésében. 

1. A Azure Portal navigáljon az **előfizetések**elemre.
1. Válassza ki az Azure-adatmegosztáshoz használt előfizetést.
1. Kattintson az **erőforrás-szolgáltatók**elemre.
1. Keressen rá a Microsoft. DataShare kifejezésre.
1. Kattintson a **regisztrálás**gombra.

## <a name="next-steps"></a>További lépések

- További információ az Azure-beli szerepkörökről – a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md)

