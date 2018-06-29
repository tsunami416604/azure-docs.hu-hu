---
title: Az RBAC segítségével kezelheti a hozzáférési jogosultsága ahhoz, hogy Azure Storage-tárolók és a várólisták (előzetes verzió) |} Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés (RBA) rendelhet szerepköröket a hozzáférés segítségével a felhasználók, csoportok, alkalmazás szolgáltatásnevekről vagy felügyelt szolgáltatás-identitások az Azure Storage-adatokat. Az Azure Storage támogatja beépített és egyéni szerepkör-tárolók és a várólisták hozzáférési jogosultsággal.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: 241808e0a7bde1d2c53cd0af1de677275c169214
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082230"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>Az RBAC (előzetes verzió) Azure Storage-adatokhoz való hozzáférési jogosultságaik kezelését

Azure Active Directory (Azure AD) engedélyezi a hozzáférési jogosultsága ahhoz, hogy a védett erőforrások [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Az Azure Storage-tárolók és a várólisták eléréséhez használt engedélyek közös készleteinek foglalnia beépített RBAC szerepkörtől álló készletet határoz meg. Ha RBAC szerepet hozzá van rendelve egy Azure AD identity, hogy identitás ezeket az erőforrásokat, hozzáférést kap a megadott hatókör szerint. Hozzáférés hatóköre beállítható úgy, az előfizetés, az erőforráscsoport, a tárfiók, vagy egy egyéni tároló vagy várólista szintjére. Hozzáférési jogosultsága az Azure Storage-erőforrások az Azure portál, az Azure parancssori eszközök és az Azure szolgáltatásfelügyeleti API használatával rendelhet hozzá. 

Az Azure AD identity lehetséges, hogy egy felhasználó, csoport vagy alkalmazás egyszerű, vagy lehet, hogy egy *identitás*. Egy rendszerbiztonsági tag felhasználó, csoport vagy szolgáltatás egyszerű alkalmazás lehet. A [identitás](../../active-directory/managed-service-identity/overview.md) való hitelesítéshez szükséges az Azure virtuális gépeken, függvény alkalmazások, virtuálisgép-méretezési csoportok és mások számára az alkalmazások automatikusan felügyelt identitás. Az Azure AD identity áttekintését lásd: [megértéséhez Azure identitáskezelési megoldásairól](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions).

## <a name="rbac-roles-for-azure-storage"></a>Az Azure Storage RBAC-szerepkörök

Az Azure Storage mind beépített és egyéni RBAC-szerepkörök támogatja. Az Azure Storage ezek beépített RBAC-szerepkörök használható az Azure AD kínál:

- [Tárolási Blob adatok közreműködői (előzetes verzió)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Tárolási Blob Adatolvasó (előzetes verzió)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Tároló várólista adatok közreműködői (előzetes verzió)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Tároló várólista Adatolvasó (előzetes verzió)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

További információ a beépített szerepkörök határozza meg az Azure Storage című [megérteni a szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

Egyéni szerepkörök tárolók és a várólisták való használatra is meghatározhat. További információkért lásd: [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md). 

> [!IMPORTANT]
> Ez az előzetes kiadás csak nem üzemi használatra készült. Éles szolgáltatásiszint-szerződések (SLA) nem lesz elérhető, amíg az Azure AD integrálása az Azure Storage általánosan elérhető van deklarálva. Ha az Azure AD-integrációs jelenleg nem támogatott a forgatókönyvnek, továbbra is használhatja a megosztott kulcsos engedélyezési vagy SAS-tokenje az alkalmazásokban. Az előzetes kapcsolatos további információkért lásd: [hitelesíti a hozzáférést az Azure Active Directoryval (előzetes verzió) Azure Storage](storage-auth-aad.md).
>
> Az előzetes RBAC szerepkör-hozzárendelések terjesztése akár öt percet is eltarthat.

## <a name="assign-a-role-to-a-security-principal"></a>Szerepkör hozzárendelése egy rendszerbiztonsági tag

Az RBAC szerepkör hozzárendelése az Azure-identitás-tárolók és a várólisták a tárfiókban lévő engedélyt. A tárfiók, vagy egy adott tárolóhoz vagy a várólista a szerepkör-hozzárendelés hatókörét megadhatja. A következő táblázat összefoglalja a beépített szerepkörök, attól függően, hogy a hatókör hozzáférési engedélyek: 

|                                 |     A BLOB adatainak közreműködő                                                 |     A BLOB Adatolvasó                                                |     Várólista adatok közreműködő                                  |     Várólista Adatolvasó                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    Előfizetés hatóköre       |    Olvasási/írási hozzáférést minden tárolók és blobok az előfizetéshez       |    Olvasási hozzáférés minden tárolók és blobok az előfizetéshez       |    Az előfizetés az összes várólistán olvasási/írási hozzáférést       |    Az előfizetés az összes várólistán olvasási hozzáférés         |
|    Erőforráscsoport hatóköre     |    Olvasási/írási hozzáférést minden tárolók és blobok az erőforráscsoportban     |    Olvasási hozzáférés minden tárolók és blobok az erőforráscsoportban     |    Az erőforráscsoport az összes várólistán olvasási/írási hozzáférést     |    Olvasási hozzáférés az összes várólistán található az erőforráscsoport     |
|    A tárfiók hatóköre    |    Minden tárolók és blobok a tárfiókban lévő olvasási/írási hozzáférést    |    Olvasási hozzáférés minden tárolók és blobok a tárfiókban lévő    |    Olvasási/írási hozzáférést a tárfiókban lévő összes várólistán    |    Olvasási hozzáférés a tárfiókban lévő összes várólistán    |
|    Tároló/várólista hatóköre    |    Olvasási/írási hozzáférést a megadott tároló és a benne található blobokat              |    Olvasási hozzáférés a megadott tároló és a benne található blobokat              |    A megadott várólista olvasási/írási hozzáférése                  |    A megadott várólista olvasási hozzáférés                    |

> [!NOTE]
> Az Azure Storage-fiókjának tulajdonos nem automatikusan rendelt engedélyek adatok eléréséhez. Meg kell explicit módon saját kezűleg egy Szerepalapú szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoportot, tárfiókot, vagy a tároló vagy várólista szintjén hozzá lehet rendelni.

Azure Storage-műveletek meghívásához szükséges engedélyekkel, lásd: [REST műveleteinek hívása engedélyeinek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

A következő szakaszok bemutatják, hogyan szerepkör hatóköre a tárfiók meg vagy egy egyéni tárolóhoz.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>A szerepkör hatóköre a storage-fiókot az Azure-portálon a hozzárendelése

Minden-tárolók és a storage-fiókot az Azure-portálon várólistákból való hozzáférés biztosítása a beépített szerepkör hozzárendelése:

1. Az a [Azure-portálon](https://portal.azure.com), lépjen a tárfiókhoz.
2. Válassza ki a tárfiókot, majd válasszon **hozzáférés-vezérlés (IAM)** hozzáférés-vezérlési beállításokkal a fiókhoz tartozó megjeleníthető. Kattintson a **Hozzáadás** gombra kattintva adja hozzá egy új szerepkört.

    ![Tároló hozzáférés-vezérlési beállításokkal ábrázoló képernyőfelvétel](media/storage-auth-aad-rbac/portal-access-control.png)

3. Az a **engedélyek hozzáadása** ablakban válassza ki a szerepkört az Azure AD identity hozzárendelése. Kereshet az identitás, akiknek adott szerepkörhöz hozzárendelni kívánt kereséséhez. Például az alábbi képen látható a **Adatolvasó a tárolási Blob (előzetes verzió)** a felhasználóhoz rendelt szerepkör.

    ![Képernyőfelvétel a változásszinkronizálás bemutatja, hogyan egy Szerepalapú szerepkör hozzárendelése](media/storage-auth-aad-rbac/add-rbac-role.png)

4. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepét, hogy a szerepkör alatt megjelenik. Például a következő kép bemutatja, hogy a felhasználók felvétele most már rendelkezik olvasási engedéllyel a tárfiókban lévő összes blob adatokhoz.

    ![Képernyőfelvétel megjelenítő listában az adott szerepkörhöz rendelt felhasználók](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Egy tároló vagy az Azure portálon várólista hatókörű szerepkör hozzárendelése

A tárolót, vagy annak a várólistára hatókörű beépített szerepkör hozzárendelése a lépések hasonlóak. Az itt bemutatott eljárás hozzárendel egy tárolót hatókörű szerepkört, de annak a várólistára hatókörű szerepkör ugyanazokat a lépéseket követheti: 

1. Az a [Azure-portálon](https://portal.azure.com), lépjen a tárfiókhoz, és megjeleníti a **áttekintése** a fiókhoz.
2. A Blob szolgáltatás, válassza a **Tallózás Blobok**. 
3. Keresse meg a legyen szerepkör tároló, és megjelenítheti a tároló beállításait. 
4. Válassza ki **hozzáférés-vezérlés (IAM)** a tároló hozzáférés-vezérlési beállítások megjelenítéséhez.
5. Az a **engedélyek hozzáadása** ablakban válassza ki a szerepkört egy Azure AD identity hozzárendelni kívánt. Ezután keresse meg az adott szerepkörhöz hozzárendelni kívánt azonosító.
6. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepét, hogy a szerepkör alatt megjelenik. Például a következő kép bemutatja, hogy a hozzáadott felhasználó most már rendelkezik olvasási engedéllyel a nevű tárolóban lévő adatok *minta-tároló*.

    ![Képernyőfelvétel megjelenítő listában az adott szerepkörhöz rendelt felhasználók](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>További lépések

- Az RBAC kapcsolatos további információkért lásd: [Ismerkedés a szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md).
- Megtudhatja, hogyan meg és kezelheti a szerepkör-hozzárendelések RBAC Azure PowerShell, az Azure parancssori felület vagy a REST API-t, olvassa el az alábbi cikkek:
    - [Szerepköralapú hozzáférés-vezérlést (RBAC) az Azure PowerShell kezelése](../../role-based-access-control/role-assignments-powershell.md)
    - [Az Azure parancssori felület szerepköralapú hozzáférés-vezérlés (RBAC) kezelése](../../role-based-access-control/role-assignments-cli.md)
    - [A REST API szerepköralapú hozzáférés-vezérlés (RBAC) kezelése](../../role-based-access-control/role-assignments-rest.md)
- A tárolók és a tárolási alkalmazásokon belül a várólista elérésének hitelesítéséhez, lásd: [Azure Storage-alkalmazások az Azure AD használatára](storage-auth-aad-app.md).
- További információt az Azure AD integrálása az Azure-tárolók és a várólisták, az Azure Storage csapat ismertető blogbejegyzésben talál, [az Azure betekintő AD hitelesítés az Azure Storage bejelentése](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
- 