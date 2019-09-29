---
title: A Azure Portal használata a blob-vagy üzenetsor-adattárolók eléréséhez – Azure Storage
description: Ha a Azure Portal használatával fér hozzá a blobhoz vagy a várólistához, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba érkező kérések hitelesítése és engedélyezése az Azure AD-fiók vagy a Storage-fiók elérési kulcsa alapján lehetséges.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3d5d5bd6400fdf09216124665b6b236b4da2583b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673289"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>A blob-vagy üzenetsor-információ elérésének Azure Portal használata

Ha a [Azure Portal](https://portal.azure.com)használatával fér hozzá a blobhoz vagy a várólistához, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba irányuló kérelem az Azure AD-fiók vagy a Storage-fiók hozzáférési kulcsa alapján engedélyezhető. A portál jelzi, hogy melyik módszert használja, és lehetővé teszi a kettő közötti váltást, ha rendelkezik a megfelelő engedélyekkel.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>A blob-vagy üzenetsor-adateléréshez szükséges engedélyek

Attól függően, hogy hogyan kívánja engedélyezni a blob-vagy üzenetsor-információhoz való hozzáférést a Azure Portalban, egyedi engedélyekre van szüksége. A legtöbb esetben ezek az engedélyek szerepköralapú hozzáférés-vezérlésen (RBAC) keresztül érhetők el. További információ a RBAC: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Fiók-hozzáférési kulcs

A blob-és üzenetsor-információk eléréséhez a fiók elérési kulcsához hozzá kell rendelnie egy RBAC-szerepkört, amely magában foglalja a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/Action**RBAC műveletet. Ez a RBAC-szerepkör beépített vagy egyéni szerepkör lehet. A **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet** támogató beépített szerepkörök a következők:

- A Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- A Azure Resource Manager [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Storage-fiók közreműködői](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepköre

Amikor megpróbál hozzáférni a blob-vagy üzenetsor-információhoz a Azure Portalban, a portál először ellenőrzi, hogy van-e hozzárendelve szerepkör a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/művelethez**. Ha ezzel a művelettel társított egy szerepkört, akkor a portál a fiók kulcsát használja a blob-és üzenetsor-adatok eléréséhez. Ha nem rendelt hozzá szerepkört ezzel a művelettel, akkor a portál az Azure AD-fiókjával próbál hozzáférni az adataihoz.

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök szolgáltatás rendszergazdája és a társ-rendszergazda a Azure Resource Manager [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörének megfelelőt adja meg. A **tulajdonosi** szerepkör magában foglalja az összes műveletet, beleértve a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is elérheti a blob-és üzenetsor-adataikat a fiók kulcsával. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Azure AD-fiók

Ha az Azure AD-fiókkal szeretné elérni a Azure Portal blob-vagy üzenetsor-adatait, az alábbi utasítások mindegyikének igaznak kell lennie az Ön számára:

- A Azure Resource Manager [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört legalább a Storage-fiók szintjére vagy magasabbra kell rendelni. Az **olvasó** szerepkör biztosítja a legtöbb korlátozott engedélyt, de egy másik Azure Resource Manager szerepkör is elfogadható, amely hozzáférést biztosít a Storage-fiókok felügyeleti erőforrásaihoz.
- Olyan beépített vagy egyéni szerepkört rendelt hozzá, amely hozzáférést biztosít a blob-vagy üzenetsor-adathoz.

Az **olvasó** szerepkör-hozzárendelés vagy egy másik Azure Resource Manager szerepkör-hozzárendelés szükséges ahhoz, hogy a felhasználó megtekinthesse és navigáljon a tárolási fiókok felügyeleti erőforrásaihoz a Azure Portal. A blob-vagy üzenetsor-adatelérést biztosító RBAC-szerepkörök nem biztosítanak hozzáférést a Storage-fiókok felügyeleti erőforrásaihoz. A blob-vagy üzenetsor-információk a portálon való eléréséhez a felhasználónak engedélyre van szüksége a Storage-fiók erőforrásainak böngészéséhez. További információ erről a követelményről: [az olvasó szerepkör kiosztása a portálhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

A blob vagy üzenetsor adataihoz való hozzáférést támogató beépített szerepkörök a következők:

- [Storage blob-adattulajdonos](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): A Azure Data Lake Storage Gen2 POSIX-hozzáférés-vezérléséhez.
- [Storage-blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blobok olvasási/írási/törlési engedélyei.
- [Storage blob-Adatolvasó](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Írásvédett engedélyek a blobokhoz.
- [Tárolási várólista adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): A várólistákhoz tartozó olvasási/írási/törlési engedélyek.
- [Tárolási várólista Adatolvasója](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): Írásvédett engedélyek a várólistákhoz.
    
Az egyéni szerepkörök a beépített szerepkörök által biztosított azonos engedélyek különböző kombinációit támogatják. Az egyéni RBAC-szerepkörök létrehozásával kapcsolatos további információkért lásd: [Egyéni szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/custom-roles.md) és [Az Azure-erőforrások szerepkör-definícióinak megismerése](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörrel rendelkező várólisták listázása nem támogatott. A várólisták listázásához a felhasználónak hozzá kell rendelnie a Azure Resource Manager **olvasó** szerepkört, a **tárolási üzenetsor Adatolvasó** szerepkörét vagy a **tárolási várólista adatközreműködői** szerepkörét.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigáljon a portálon lévő blobokhoz vagy várólistákhoz

A blob-vagy üzenetsor-információk a portálon való megtekintéséhez navigáljon a Storage-fiók **áttekintéséhez** , és kattintson a **Blobok** vagy **várólisták**hivatkozásaira. Másik lehetőségként megnyithatja a menü **blob Service** és **Queue szolgáltatás** részeit. 

![Navigáljon a blob-vagy üzenetsor-értékekhez a Azure Portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Az aktuális hitelesítési módszer meghatározása

Amikor megnyit egy tárolót vagy várólistát, a Azure Portal jelzi, hogy jelenleg a fiók-hozzáférési kulcsot vagy az Azure AD-fiókot használja a hitelesítéshez.

Az ebben a szakaszban szereplő példák egy tároló és a hozzá tartozó Blobok elérését mutatják be, de a portál ugyanazt az üzenetet jeleníti meg, amikor a várólista és az üzeneteinek elérését, illetve a várólisták listáját is megjeleníti.

### <a name="account-access-key"></a>Fiók-hozzáférési kulcs

Ha a fiók-hozzáférési kulccsal végzi a hitelesítést, a hitelesítési módszerként megadott **elérési kulcsot** fogja látni a portálon:

![Jelenleg a Container-adatok elérése a fiók kulcsával](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Ha az Azure AD-fiók használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik a megfelelő engedélyekkel az Ön által hozzárendelt RBAC-szerepkörökön keresztül, folytathatja a folytatást. Ha azonban nem rendelkezik a megfelelő engedélyekkel, a következőhöz hasonló hibaüzenet jelenik meg:

![Hiba jelenik meg, ha az Azure AD-fiók nem támogatja a hozzáférést](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Figyelje meg, hogy nem jelennek meg Blobok a listában, ha az Azure AD-fiókja nem rendelkezik a megtekintéséhez szükséges engedélyekkel. Kattintson a **kapcsolóra** a Hívóbetű eléréséhez, és használja a hozzáférési kulcsot a hitelesítéshez.

### <a name="azure-ad-account"></a>Azure AD-fiók

Ha az Azure AD-fiókkal végzi a hitelesítést, akkor a portálon hitelesítési módszerként megadott **Azure ad-felhasználói fiókot** fogja látni:

![Jelenleg a Container-adatok elérése az Azure AD-fiókkal](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Ha a fiók-hozzáférési kulcs használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik hozzáféréssel a fiók kulcsához, akkor folytathatja a folytatást. Ha azonban nincs hozzáférése a fiók kulcsához, a következőhöz hasonló hibaüzenet jelenik meg:

![Hiba jelenik meg, ha nincs hozzáférése a fiók kulcsához](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Figyelje meg, hogy a listában nem jelennek meg Blobok, ha nem rendelkezik hozzáféréssel a fiók kulcsaihoz. Kattintson a **Váltás az Azure ad felhasználói fiókra** hivatkozásra az Azure ad-fiók ismételt hitelesítésre való használatához.

## <a name="next-steps"></a>További lépések

- [Azure-blobok és-várólisták hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md)
- [Hozzáférés biztosítása az Azure-tárolók és-várólisták számára a Azure Portal RBAC](storage-auth-aad-rbac-portal.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure CLI-vel](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával a PowerShell-lel](storage-auth-aad-rbac-powershell.md)
