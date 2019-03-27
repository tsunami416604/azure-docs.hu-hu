---
title: Blob eléréséhez, vagy a várólista-adatok – Azure Storage az Azure portal használatával
description: Ha blob fér hozzá, vagy a sorban lévő adatok az Azure portal használatával, a portálon teszi kéréseket az Azure Storage a háttérben. Ezek a kérelmek, az Azure Storage hitelesíthetők, és engedélyezi a használatával az Azure AD-fiókot, vagy a tárfiók hozzáférési kulcsát.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5a4426b665c58dd2c0bf7f5b5b41ebfca158e782
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444725"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Az Azure Portallal blob és üzenetsor adatok elérését

Amikor blob vagy üzenetsor használatával hozzáfér a [az Azure portal](https://portal.azure.com), valójában a portálon teszi az Azure Storage-kérelmeket. Ezek a kérelmek, az Azure Storage hitelesíthetők, és engedélyezi a használatával az Azure AD-fiókot, vagy a tárfiók hozzáférési kulcsát. A portálon azt jelzi, hogy melyik hitelesítési módszert használ, és lehetővé teszi, hogy a kettő közötti váltás, ha rendelkezik a megfelelő engedélyekkel.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Blob és üzenetsor-adatokhoz való hozzáféréshez szükséges engedélyek

Attól függően, hogyan szeretné hitelesíteni a hozzáférést blob vagy üzenetsor adatokat az Azure Portalon adott engedélyekre lesz szüksége. A legtöbb esetben ezek az engedélyek biztosítják, szerepköralapú hozzáférés-vezérlés (RBAC) keresztül. RBAC kapcsolatos további információkért lásd: [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Tárfiók elérési kulcsa

Blob és üzenetsor-adatok elérése a fiók hozzáférési kulcsára, rendelkeznie kell egy Önhöz hozzárendelt RBAC szerepkör, amely tartalmazza az RBAC-művelet **Microsoft.Storage/storageAccounts/listkeys/action**. Az RBAC szerepkör beépített vagy egyéni szerepkör lehet. Beépített szerepkörök, amelyek támogatják a **Microsoft.Storage/storageAccounts/listkeys/action** tartalmazza:

- Az Azure Resource Manager [tulajdonosa](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- Az Azure Resource Manager [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [Tárfiók-közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor) szerepkör

Próbál elérni a blob- vagy várólista adatokat az Azure Portalon, ha a portálon először ellenőrzi, hogy Önhöz rendelve egy olyan szerepkörhöz **Microsoft.Storage/storageAccounts/listkeys/action**. Ha rendelkezik ezzel a művelettel szerepkört rendeltek, majd a portál alkalmaz a fiókkulcs blob és üzenetsor adatainak eléréséhez. Ha rendelkezik nincs hozzárendelve egy szerepkörhöz, ezt a műveletet, majd a portál megpróbál hozzáférni adatokat az Azure AD-fiókot.

> [!NOTE]
> A hagyományos előfizetés-rendszergazdai szerepköröket, szolgáltatás-rendszergazda és a társ-rendszergazdaként közé tartozik az Azure Resource Manager-megfelelője [tulajdonosa](../../role-based-access-control/built-in-roles.md#owner) szerepkör. A **tulajdonosa** szerepkör tartalmaz minden olyan művelet, beleértve a **Microsoft.Storage/storageAccounts/listkeys/action**, így a felhasználó ezen rendszergazdai szerepkörök egyike a blob és üzenetsor adatok is elérheti a fiókkulcs. További információkért lásd: [klasszikus előfizetés rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Azure AD-fiók

Blob és üzenetsor adatok eléréséhez az Azure Portalról az Azure AD-fiókot használ, mindkét az alábbi utasításokat kell teljesülniük az Ön számára:

- Az Azure Resource Manager társított meg [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkör minimális szintre a tárfiók hatókörrel rendelkező vagy újabb. A **olvasó** szerepkör engedélyt ad a legtöbb korlátozott, de egy másik Azure Resource Manager-szerepkör, amely hozzáférést biztosít a tárfiók kezelési erőforrásainak is fogadható el.
- Akár egy beépített vagy egyéni szerepkört, blobok és a sorban lévő adatok elérését biztosító Önhöz rendelve.

A **olvasó** szerepkör-hozzárendelés vagy egy másik Azure-erőforrás-kezelő szerepkör-hozzárendelés azért szükséges, hogy a felhasználó megtekintheti és keresse meg a tárfiók kezelési erőforrásainak az Azure Portalon. A blob és üzenetsor-adatokhoz hozzáférést biztosító RBAC-szerepkörök nem biztosíthat hozzáférést tárfiók kezelési erőforrásainak. A portálon a blob és üzenetsor adatok eléréséhez, a felhasználó engedélyre van szüksége, keresse meg a tárfiók erőforrásainak. Ezzel a követelménnyel kapcsolatban további információkért lásd: [a portál elérésére szolgáló olvasó szerepkört](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

A beépített szerepkörök, amelyek támogatják a blob és üzenetsor-adatokhoz való hozzáférést a következők:

- [Tárolási Blob adatok tulajdonosa](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): A POSIX hozzáférés-vezérlés az Azure Data Lake Storage Gen2 (előzetes verzió).
- [Storage-Blobadatok Közreműködője](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Blobok olvasási, írási és törlési engedélyek.
- [Storage-Blobadatok olvasója](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Blobok csak olvasható engedélyek.
- [Storage-Üzenetsorbeli adatok Közreműködője](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Olvasási, írási és törlési engedélyek sorra.
- [Storage-Üzenetsorbeli adatok olvasója](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Üzenetsorok csak olvasható engedélyek.
    
Egyéni szerepkör ugyanazokkal az engedélyekkel a beépített szerepkörök által biztosított különböző kombinációit is támogatja. Egyéni RBAC-szerepkörök létrehozásával kapcsolatos további információkért lásd: [egyéni szerepkörök az Azure-erőforrások](../../role-based-access-control/custom-roles.md) és [megismerheti az Azure-erőforrások szerepkör-definíciók](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Egy hagyományos előfizetés-rendszergazda szerepkörrel rendelkező üzenetsorok listázása nem támogatott. Listázásához üzenetsorok, a felhasználó hozzárendelt kell őket az Azure Resource Manager **olvasó** szerepkör, a **tárolási Üzenetsorbeli adatok olvasója** szerepkör, vagy a **tárolási Üzenetsorbeli adatok Közreműködője** szerepkör.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigáljon a blobok vagy üzenetsorok a portálon

Blob és üzenetsor-adatok megtekintése a portálon, lépjen a **áttekintése** a tárfiókhoz, és kattintson a hivatkozások a **Blobok** vagy **üzenetsorok**. Alternatív megoldásként navigálhat a **Blob service** és **Queue szolgáltatás** szakaszok a menüben. 

![Keresse meg a blob- vagy várólista adatokat az Azure Portalon](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>A jelenlegi hitelesítési módszer meghatározása

Navigálás egy tároló vagy egy üzenetsor, az Azure portal jelzi, e jelenleg használ a tárelérési kulccsal vagy az Azure AD-fiók hitelesítéséhez.

Ebben a szakaszban szereplő példák megjelenítése fér hozzá a tárolóhoz és annak blobjaihoz, de a portál megjeleníti ugyanazt az üzenetet egy üzenetsorba, és az üzenetek eléréséhez, vagy várólisták listázása.

### <a name="account-access-key"></a>Tárfiók elérési kulcsa

Ha a fiók hozzáférési kulcs használatával hitelesít, látni fogja **hívóbetű** megadott hitelesítési módszerként a portálon:

![Jelenleg a fiók kulccsal a tároló adatok elérése](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Váltson Azure AD-fiókot, kattintson a hivatkozásra az ábrán vannak kiemelve. Ha rendelkezik a megfelelő engedélyekkel az RBAC-szerepkörök Önhöz rendelt keresztül, a folytatáshoz fogja. Azonban ha nem rendelkezik a megfelelő engedélyekkel, az alábbihoz hasonló hibaüzenetet láthatja:

![Hibaüzenet jelenik meg, ha az Azure AD-fiók nem támogatja a hozzáférést](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Figyelje meg, hogy nem BLOB szerepelnek a listán, ha az Azure AD-fiókot nem rendelkezik engedélyekkel a megtekintésükhöz. Kattintson a **Váltás a hozzáférési kulcs** hivatkozás újból a hitelesítést a hozzáférési kulcs használatára.

### <a name="azure-ad-account"></a>Azure AD-fiók

Ha Ön végzik a hitelesítést az Azure AD-fiókot használ, látni fogja **Azure AD felhasználói fiók** megadott hitelesítési módszerként a portálon:

![Jelenleg a tároló adatok az Azure AD-fiók elérése](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

A fiók hozzáférési kulccsal való váltáshoz kattintson a hivatkozásra, az ábrán vannak kiemelve. Ha rendelkezik hozzáféréssel a fiók kulcsára, majd meg fogja a folytatáshoz. Azonban ha nem áll rendelkezésre az a fiók kulcsára, látni fogja az alábbihoz hasonló hibaüzenetet:

![Hibaüzenet jelenik meg, ha nem rendelkezik hozzáféréssel a fiókkulcs](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Figyelje meg, hogy nem BLOB szerepelnek a listán, ha nem rendelkezik hozzáféréssel a fiókkulcsok. Kattintson a **váltani az Azure AD felhasználói fiók** újból a hitelesítést az Azure AD-fiókot használni mutató hivatkozást.

## <a name="next-steps"></a>További lépések

- [Hitelesíti a hozzáférést az Azure-blobok és üzenetsorok az Azure Active Directoryval](storage-auth-aad.md)
- [Hozzáférés engedélyezése az Azure-tárolók és a várólisták RBAC használata az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Az RBAC Azure CLI-vel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-cli.md)
- [Az RBAC a PowerShell-lel az Azure blob és üzenetsor adatokhoz való hozzáférés engedélyezése](storage-auth-aad-rbac-powershell.md)