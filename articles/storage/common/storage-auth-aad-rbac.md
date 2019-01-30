---
title: Az RBAC használatával a tárolókhoz és az Azure Storage (előzetes verzió) – üzenetsorok hozzáférési jogosultságok kezelése |} A Microsoft Docs
description: Szerepköralapú hozzáférés-vezérlés (RBAC) hozzáférés szerepkörök hozzárendelése használatával a felhasználók, csoportok, alkalmazások szolgáltatásnevének vagy felügyelt szolgáltatásidentitások blob és üzenetsor adatokat. Az Azure Storage támogatja a beépített és egyéni szerepkörök hozzáférési jogosultságokat a tárolókhoz és üzenetsorok.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 6cecb2d5fde4fc651cf0ba971189ef62fa330c13
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241709"
---
# <a name="manage-access-rights-to-azure-blob-and-queue-data-with-rbac-preview"></a>Hozzáférési jogosultsággal az Azure Blob- és üzenetsoradatot RBAC (előzetes verzió) használata kezelheti

Az Azure Active Directory (Azure AD) keresztül biztonságos erőforrásokhoz való hozzáférési jogosultságok engedélyezi [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview). Az Azure Storage határozza meg, amely magában foglalja a tárolók és -várólisták eléréséhez használt gyakori jogosultságkészletek beépített RBAC-szerepkör. Amikor egy RBAC szerepkör van rendelve egy Azure AD identity, hogy identitás hozzáférést kap az érintett erőforrásokra, a megadott hatókör alapján. Hozzáférés az előfizetéshez, az erőforráscsoport, a storage-fiókban vagy egy tároló vagy üzenetsor szintjét hatóköre. Az Azure portal, az Azure parancssori eszközeivel és az Azure felügyeleti API-k használatával az Azure Storage-erőforrások hozzáférési jogosultságokat rendelhet. 

Az Azure AD identity előfordulhat, hogy egy felhasználó, csoport vagy alkalmazás egyszerű szolgáltatást, vagy egy felügyelt identitás, az Azure-erőforrásokhoz lehet. A rendszerbiztonsági tag lehet egy felhasználó, csoport vagy alkalmazás egyszerű szolgáltatást. A [-identitás az Azure-erőforrások](../../active-directory/managed-identities-azure-resources/overview.md) hitelesítéséhez az Azure virtual machines, a függvényalkalmazások, a virtual machine scale sets és mások a futó alkalmazásokból egy olyan automatikus felügyelt identitás. Az Azure AD identity áttekintését lásd: [ismertetése Azure identitáskezelési megoldásairól](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>A blobok és üzenetsorok RBAC-szerepkörök

Az Azure Storage támogatja a beépített és az egyéni RBAC-szerepkörökhöz. Az Azure Storage ezeket az Azure ad-vel használható beépített RBAC-szerepkör kínál:

- [Tárolási Blob adatok tulajdonosa (előzetes verzió)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview): Állítsa be a tulajdonosi és a POSIX hozzáférés-vezérlés kezelése az Azure Data Lake Storage Gen2 (előzetes verzió). További információkért lásd: [hozzáférés-vezérlés az Azure Data Lake Storage Gen2](../blobs/data-lake-storage-access-control.md).
- [Storage-Blobadatok Közreműködője (előzetes verzió)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview): A Blob storage-erőforrások olvasási, írási és törlési engedélyek használatával.
- [Storage-Blobadatok olvasója (előzetes verzió)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview): Használja a Blob storage-erőforrások csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője (előzetes verzió)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview): Olvasási, írási és törlési engedélyt az Azure-üzenetsorok használatával.
- [Storage-Üzenetsorbeli adatok olvasója (előzetes verzió)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview): Csak olvasási engedélyeket az Azure-üzenetsorok használatával.

További információ a beépített szerepkörök határozza meg az Azure Storage, lásd: [megismerheti a szerepkör-definíciók](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview).

A tárolók és a várólisták segítségével egyéni szerepköröket is meghatározhat. További információkért lásd: [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="assign-a-role-to-a-security-principal"></a>Szerepkör hozzárendelése a rendszerbiztonsági tag

Az RBAC szerepkör hozzárendelése egy Azure-identitás engedélyeket lehet biztosítani a tárolók vagy a tárfiókban lévő üzenetsorok. A szerepkör-hozzárendelést a storage-fiókot, vagy egy adott tároló vagy egy üzenetsor gazdagépcsoportjaira. A következő táblázat összefoglalja a hatókör függően a beépített szerepkörök, hozzáférési jogot:

|Hatókör|BLOB adatok tulajdonosa|Blobadatok Közreműködője|Blob Data Reader|Üzenetsorbeli adatok Közreműködője|Queue Data Reader|
|---|---|---|---|---|---|
|Előfizetési szinten|Olvasási/írási hozzáférést és a POSIX hozzáférés-vezérlési felügyeleti a tárolók és blobok az előfizetésben|Tárolók és blobok az előfizetésben az olvasási/írási hozzáférést| Olvasási hozzáférés tárolók és blobok az előfizetésben|Olvasási/írási hozzáférést az előfizetés összes várólista|Olvasási hozzáférés az előfizetésben található összes várólista|
|Erőforráscsoport szintjén|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlési felügyeleti a tárolók és blobok az erőforráscsoportban|Olvasási/írási hozzáférést a tárolók és blobok az erőforráscsoportban|Olvasási hozzáférés tárolók és blobok az erőforráscsoportban|Olvasási/írási hozzáférés az erőforráscsoportban lévő összes várólista|Olvasási hozzáférés az erőforráscsoportban lévő összes üzenetsor|
|Storage-fiók szintjén|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlés kezelése a tárolókhoz és a BLOB storage-fiókban|Olvasási/írási hozzáférést a tárolókhoz és a BLOB storage-fiókban|Olvasási hozzáférés tárolók és a BLOB storage-fiókban|Olvasási/írási hozzáférést a storage-fiókban lévő összes várólista|Olvasási hozzáférés a tárfiókban lévő összes üzenetsor|
|Tároló/várólista szint|Olvasási/írási hozzáféréssel és a POSIX hozzáférés-vezérlési kezelési a megadott tárolóhoz és annak blobjaihoz.|Olvasási/írási hozzáférést a megadott tárolóhoz és annak blobjaihoz|A megadott tárolóhoz és annak blobjaihoz olvasási hozzáférés|Olvasási/írási hozzáférést a megadott várólista|Olvasási hozzáférés a megadott várólista|

> [!NOTE]
> Az Azure Storage-fiók tulajdonosai akkor nem lesznek automatikusan hozzárendelve engedélyeket az adatok eléréséhez. Kell explicit módon saját magának egy RBAC szerepkör hozzárendelése az Azure Storage. Az előfizetés, erőforráscsoport, tárfiók, vagy egy tároló vagy üzenetsor szintjén rendelhet.

További információ az Azure Storage-műveletek meghívásához szükséges engedélyekről: [REST-műveleteinek meghívására szolgáló engedélyek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).

A következő szakaszok bemutatják, hogyan rendelhet egy szerepkört tároló alkalmazáskészletre vagy a storage-fiók hatókörébe.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>A storage-fiókba az Azure Portalon hatókörű szerepkör hozzárendelése

Minden tárolók vagy az Azure Portalon a tárfiókban lévő üzenetsorok való hozzáférést beépített szerepkör hozzárendelése:

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz.
1. Válassza ki a tárfiókot, majd válassza ki **hozzáférés-vezérlés (IAM)** a fiókhoz tartozó hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.

    ![Tárolási hozzáférés-vezérlési beállításokkal ábrázoló képernyőfelvétel](media/storage-auth-aad-rbac/portal-access-control.png)

1. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a szerepkört az Azure AD identitás hozzárendelése. Majd keresse meg az identitást, akiknek az adott szerepkörhöz hozzárendelni kívánt keresse. Ha például az alábbi képen a **Storage-Blobadatok olvasója (előzetes verzió)** a felhasználóhoz rendelt szerepkör.

    ![Az RBAC szerepkör hozzárendelése bemutató képernyőkép](media/storage-auth-aad-rbac/add-rbac-role.png)

1. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepköre alatt a szerepkör a listán jelenik meg. Ha például az alábbi képen látható, hogy a hozzáadott felhasználó már rendelkezik olvasási engedéllyel a tárfiókban lévő összes blob adatokat.

    ![Képernyőn egy szerepkörhöz rendelt felhasználók listáját bemutató képernyőkép](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Egy tároló vagy az Azure Portalon várólista hatóköre szerepkör hozzárendelése

> [!IMPORTANT]
> Ezt megteheti egy fiókot használja a hierarchikus névtér esetében engedélyezve van, még ha nem.

Egy tároló vagy egy üzenetsor hatókörű beépített szerepkör hozzárendelése szükséges lépések hasonlóak. Az itt bemutatott eljárás hozzárendel egy szerepkör hatóköre egy tároló, de várólista hatóköre szerepkör hozzárendelése ugyanazokat a lépéseket követheti: 

1. Az a [az Azure portal](https://portal.azure.com), lépjen a tárfiókhoz, és megjeleníti a **áttekintése** a fiókhoz.
1. A szolgáltatások területen válassza ki a **Blobok**. 
1. Keresse meg a helyet, amelynek meg szeretné rendelhet egy szerepkört, és megjeleníti a tároló beállításait. 
1. Válassza ki **hozzáférés-vezérlés (IAM)** a tárolóhoz a hozzáférés-vezérlési beállítások megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapján megtekintheti a szerepkör-hozzárendelések listáját.

    ![Képernyő-tároló hozzáférés-vezérlési beállításokkal bemutató képernyőkép](media/storage-auth-aad-rbac/portal-access-control-container.png)
1. Kattintson a **szerepkör-hozzárendelés hozzáadása** gombra kattintva adhat hozzá egy új szerepkör.
1. Az a **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a szerepkört, amely egy Azure AD Identity hozzárendelni kívánt. Ezután keresse meg az identitást, amelyhez szeretné, hogy a szerepkör hozzárendelése.
1. Kattintson a **Save** (Mentés) gombra. Az identitás, akinek a szerepköre alatt a szerepkör a listán jelenik meg. Ha például az alábbi képen látható, hogy a hozzáadott felhasználó már rendelkezik olvasási engedéllyel a nevű tárolóban lévő adatok *minta-tároló*.

    ![Képernyőn egy szerepkörhöz rendelt felhasználók listáját bemutató képernyőkép](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>További lépések

- RBAC kapcsolatos további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).
- Ismerje meg, hogyan hozzárendelése és kezelése az Azure PowerShell, az Azure CLI vagy a REST API az RBAC szerepkör-hozzárendeléseit, tanulmányozza a következő cikkeket:
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure PowerShell használatával](../../role-based-access-control/role-assignments-powershell.md)
    - [Kezelheti a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) a REST API-val kezelheti.](../../role-based-access-control/role-assignments-rest.md)
- A tárolókhoz és üzenetsorok, a storage-alkalmazásokban való elérésének hitelesítéséhez, lásd: [az Azure AD használata az Azure Storage-alkalmazások](storage-auth-aad-app.md).
- Az Azure-tárolók és -üzenetsorok az Azure AD-integrációval kapcsolatos további információkért lásd: az Azure Storage csapat blogja tenne fel, [bejelentése a megtekintése az Azure AD-hitelesítés az Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
