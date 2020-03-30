---
title: Blob- vagy várólista-adatok eléréséhez használja az Azure Portalt
titleSuffix: Azure Storage
description: Amikor az Azure Portalhasználatával fér hozzá a blob- vagy várólista-adatokhoz, a portál a borítók alatt kéri az Azure Storage-t. Ezek a kérelmek az Azure Storage-ba hitelesíthetők és engedélyezhetők az Azure AD-fiók vagy a tárfiók hozzáférési kulcs használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866893"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Blob- vagy várólista-adatok eléréséhez használja az Azure Portalt

Amikor az [Azure Portal](https://portal.azure.com)használatával fér hozzá a blob- vagy várólista-adatokhoz, a portál a feldolgozási jog alatt kéri az Azure Storage-t. Az Azure Storage-ra vonatkozó kérés az Azure AD-fiók vagy a tárfiók hozzáférési kulcshasználatával is engedélyezhető. A portál jelzi, hogy melyik módszert használja, és lehetővé teszi a kettő közötti váltást, ha rendelkezik a megfelelő engedélyekkel.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>A blob- vagy várólista-adatok eléréséhez szükséges engedélyek

Attól függően, hogy hogyan szeretné engedélyezni a blob- vagy várólista-adatokhoz való hozzáférést az Azure Portalon, speciális engedélyekre van szüksége. A legtöbb esetben ezek az engedélyek szerepköralapú hozzáférés-vezérlésen (RBAC) keresztül érhetők el. Az RBAC-ről a [Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md)

### <a name="account-access-key"></a>Fiókhozzáférési kulcs

A blob- és várólista-adatok fiókhozzáférési kulccsal való eléréséhez rendelkeznie kell egy RBAC-szerepkörrel, amely tartalmazza a **Microsoft.Storage/storageAccounts/listkeys/action**RBAC műveletet. Ez az RBAC szerepkör lehet beépített vagy egyéni szerepkör. A **Microsoft.Storage/storageAccounts/listkeys/action** beépített szerepkörei a következők:

- Az Azure Resource Manager [owner](../../role-based-access-control/built-in-roles.md#owner) szerepkör
- Az Azure Resource Manager [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkör
- A [tárfiók közreműködői szerepköre](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Amikor megpróbál hozzáférni a blob- vagy várólista-adatokhoz az Azure Portalon, a portál először ellenőrzi, hogy van-e szerepkör a **Microsoft.Storage/storageAccounts/listkeys/action szolgáltatással.** Ha ezzel a művelettel szerepkört kapott, akkor a portál a fiókkulcsot használja a blob- és várólista-adatok eléréséhez. Ha nem kapott szerepkört ezzel a művelettel, majd a portál megkísérli az adatok elérését az Azure AD-fiók használatával.

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök Service Administrator és társ-rendszergazda tartalmazza az Azure Resource Manager [owner](../../role-based-access-control/built-in-roles.md#owner) szerepkör megfelelője. A **tulajdonosi** szerepkör tartalmazza az összes műveletet, beleértve a **Microsoft.Storage/storageAccounts/listkeys/action,** így a felhasználó egy ilyen felügyeleti szerepkörök is hozzáférhet nek blob és várólista adatait a fiókkulcs. További információt a [Klasszikus előfizetéses rendszergazdai szerepkörök című témakörben](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)talál.

### <a name="azure-ad-account"></a>Azure AD-fiók

Ha az Azure AD-fiókjával szeretné elérni a blob- vagy várólista-adatokat az Azure Portalról, mindkét alábbi utasításnak igaznak kell lennie:

- Az Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) szerepkört legalább a tárfiók vagy magasabb szintű hatókörrel kapta. Az **Olvasó** szerepkör a legtöbb korlátozott engedélyeket adja, de egy másik Azure Resource Manager szerepkör, amely hozzáférést biztosít a tárfiók-kezelési erőforrásokhoz, szintén elfogadható.
- A blob- vagy várólista-adatokhoz való hozzáférést biztosít.

A **Reader** szerepkör-hozzárendelés vagy egy másik Azure Resource Manager szerepkör-hozzárendelés szükséges ahhoz, hogy a felhasználó megtekintheti és navigálhat a tárfiók-kezelési erőforrások az Azure Portalon. A blob- vagy várólista-adatokhoz hozzáférést biztosító RBAC-szerepkörök nem biztosítanak hozzáférést a tárfiók-kezelési erőforrásokhoz. A blob- vagy várólista-adatok portálon való eléréséhez a felhasználónak engedélyekre van szüksége a tárfiók-erőforrásokban való navigáláshoz. Erről a követelményről további információt [az Olvasó szerepkör hozzárendelése a portálhoz című témakörben talál.](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)

A blob- vagy várólista-adatokhoz való hozzáférést támogató beépített szerepkörök a következők:

- [Storage Blob-adatok tulajdonosa:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)AZ Azure Data Lake Storage Gen2 POSIX hozzáférés-vezérléséhez.
- [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Olvasási/írási/törlési engedélyek blobok.
- [Storage Blob Data Reader:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)Csak olvasási engedélyek blobok.
- [Storage Queue Data Contributor](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): A várólisták olvasási/írási/törlési engedélyei.
- [Tárolóvárólista-adatolvasó:](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader)Csak olvasható engedélyek a várólistákhoz.
    
Az egyéni szerepkörök a beépített szerepkörök által biztosított engedélyek különböző kombinációit támogathatják. Az egyéni RBAC-szerepkörök létrehozásáról további információt az [Egyéni szerepkörök az Azure-erőforrásokhoz](../../role-based-access-control/custom-roles.md) és [az Azure-erőforrások szerepkör-definícióinak ismertetése című](../../role-based-access-control/role-definitions.md)témakörben talál.

A klasszikus előfizetés-rendszergazdai szerepkörrel rendelkező várólisták listázása nem támogatott. A várólisták listázásához a felhasználónak hozzá kell rendelnie az Azure Resource Manager **Reader** szerepkört, a **storage-várólista-adatolvasó** szerepkört vagy a **storage-várólista-adatközreműködői** szerepkört.

> [!IMPORTANT]
> A Storage Explorer előzetes verziója az Azure Portalon nem támogatja az Azure AD hitelesítő adatok megtekintéséhez és módosításához blob vagy várólista adatait. Az Azure Portalon található Storage Explorer mindig a fiókkulcsokat használja az adatok eléréséhez. A Storage Explorer azure-portálon való használatához hozzá kell rendelnie egy **microsoft.Storage/storageAccounts/listkeys/action szerepkört.**

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Ugrás a blobokra vagy várólistákra a portálon

Blob- vagy várólista-adatok megtekintéséhez keresse meg a tárfiók **áttekintése,** és kattintson a **blobok** vagy **várólisták hivatkozásaira.** Másik lehetőségként keresse meg a **menü Blob szolgáltatás** és **várólista szolgáltatás** szakaszait. 

![Navigálás a blob- vagy várólista-adatokra az Azure Portalon](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Az aktuális hitelesítési módszer meghatározása

Amikor egy tárolóhoz vagy egy várólistához navigál, az Azure Portal jelzi, hogy jelenleg a fiók hozzáférési kulcsát vagy az Azure AD-fiókot használja-e a hitelesítéshez.

Ebben a szakaszban a példák egy tároló és a blobok elérését mutatják be, de a portál ugyanazt az üzenetet jeleníti meg, amikor egy várólistához és annak üzeneteihez, vagy a várólisták listázásához fér hozzá.

### <a name="account-access-key"></a>Fiókhozzáférési kulcs

Ha a fiókhozzáférési kulccsal hitelesíti a hitelesítési kulcsot, a portálon hitelesítési módszerként az **Access Key** lesz megadva:

![Tárolóadatok elérése a fiókkulccsal](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Az Azure AD-fiók használatára való váltáshoz kattintson a képen kiemelt hivatkozásra. Ha rendelkezik a megfelelő engedélyekkel az Önhöz rendelt RBAC-szerepkörökön keresztül, folytathatja. Ha azonban nem rendelkezik a megfelelő engedélyekkel, a következőhöz hasonló hibaüzenet jelenik meg:

![Hiba jelenik meg, ha az Azure AD-fiók nem támogatja a hozzáférést](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Figyelje meg, hogy nem jelennek meg blobok a listában, ha az Azure AD-fiók nem rendelkezik engedélyekkel azok megtekintéséhez. Kattintson a **Kapcsoló gombra a kulcs eléréséhez** linket használni a hozzáférési kulcsot a hitelesítéshez újra.

### <a name="azure-ad-account"></a>Azure AD-fiók

Ha az Azure AD-fiókjával hitelesíti a hitelesítést, a portálon hitelesítési módszerként megadva az **Azure AD felhasználói fiók** jelenik meg:

![Tárolóadatok elérése azure AD-fiókkal](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Ha a fiókelérési kulcs használatára szeretne váltani, kattintson a képen kiemelt hivatkozásra. Ha van hozzáférése a fiókkulcshoz, akkor folytathatja. Ha azonban nem fér hozzá a fiókkulcshoz, a következőhöz hasonló hibaüzenet jelenik meg:

![Hiba jelenik meg, ha nincs hozzáférése a fiókkulcshoz](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Figyelje meg, hogy nem blobok jelennek meg a listában, ha nem rendelkezik a fiókkulcsok. Kattintson a Váltás az **Azure AD felhasználói fiókra** hivatkozásra az Azure AD-fiók ismételt hitelesítéséhez.

## <a name="next-steps"></a>További lépések

- [Az Azure-blobokhoz és -várólistákhoz való hozzáférés hitelesítése az Azure Active Directory használatával](storage-auth-aad.md)
- [Hozzáférés az Azure-tárolókhoz és várólistákhoz az RBAC segítségével az Azure Portalon](storage-auth-aad-rbac-portal.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával az Azure CLI-vel](storage-auth-aad-rbac-cli.md)
- [Hozzáférés biztosítása Azure-blobok és -üzenetsorok adataihoz RBAC használatával a PowerShell-lel](storage-auth-aad-rbac-powershell.md)
