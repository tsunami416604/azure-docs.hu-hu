---
title: Az Azure Portal használata RBAC-szerepkör hozzárendelése az adateléréshez
titleSuffix: Azure Storage
description: Megtudhatja, hogy az Azure Portal használatával hogyan rendelhet engedélyeket egy Szerepköralapú hozzáférés-vezérléssel (RBAC) rendelkező Azure Active Directory rendszerbiztonsági taghoz. Az Azure Storage támogatja a beépített és egyéni RBAC szerepkörök et az Azure AD-n keresztüli hitelesítéshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ec32990513d9199c4aaccf1bcfcbf76f348f877b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75867501"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Az Azure Portal használatával RBAC-szerepkört rendelhet a blob- és várólista-adatokhoz való hozzáféréshez

Az Azure Active Directory (Azure AD) [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)révén engedélyezi a védett erőforrásokhoz való hozzáférési jogokat. Az Azure Storage a blob- vagy várólista-adatok eléréséhez használt közös engedélyeket tartalmazó beépített RBAC-szerepkörök készletét határozza meg.

Ha egy RBAC-szerepkör egy Azure AD rendszerbiztonsági taghoz van rendelve, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz az adott rendszerbiztonsági tag számára. Az access az előfizetés, az erőforráscsoport, a tárfiók vagy egy adott tároló vagy várólista szintjére ható. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrások.](../../active-directory/managed-identities-azure-resources/overview.md)

Ez a cikk ismerteti, hogyan használhatja az Azure Portal rbac szerepkörök hozzárendelése. Az Azure Portal egy egyszerű felületet biztosít az RBAC-szerepkörök hozzárendeléséhez és a tárolási erőforrásokhoz való hozzáférés kezeléséhez. RBAC-szerepköröket is rendelhet a blob- és várólista-erőforrásokhoz az Azure parancssori eszközeivel vagy az Azure Storage felügyeleti API-k használatával. A tárolási erőforrások RBAC szerepköreiről az [Azure-blobokhoz és várólistákhoz való hozzáférés hitelesítése az Azure Active Directory használatával című](storage-auth-aad.md)témakörben talál további információt. 

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-szerepkörök blobokhoz és várólistákhoz

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforráshatókör meghatározása 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése az Azure Portal használatával

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, keresse meg az adott erőforrást az Azure Portalon. Jelenítse meg az erőforrás **hozzáférés-vezérlési (IAM)** beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

1. Rendelje hozzá a megfelelő Azure Storage RBAC szerepkört egy Azure AD rendszerbiztonsági tag hoz való hozzáférés biztosításához.

1. Rendelje hozzá az Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) szerepkört azokhoz a felhasználókhoz, akiknek az Azure Portalon keresztül az Azure AD-hitelesítő adataik használatával kell hozzáférniük a tárolókhoz vagy várólistákhoz. 

A következő szakaszok részletesebben ismertetik ezeket a lépéseket.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosaként nem kap automatikusan engedélyeket az adatok eléréséhez. Explicit módon hozzá kell rendelnie magának egy RBAC szerepkört az Azure Storage-hoz. Hozzárendelheti az előfizetés, az erőforráscsoport, a tárfiók, illetve egy tároló vagy várólista szintjén.
>
> Nem rendelhet hozzá hatókört egy tárolóhoz vagy várólistához, ha a tárfiókban engedélyezve van egy hierarchikus névtér.

### <a name="assign-a-built-in-rbac-role"></a>Beépített RBAC szerepkör hozzárendelése

Mielőtt szerepkört rendelne egy rendszerbiztonsági taghoz, mindenképpen vegye figyelembe a megadott engedélyek hatókörét. Tekintse át az [erőforráshatókör meghatározása](#determine-resource-scope) szakaszt a megfelelő hatókör meghatározásához.

Az itt látható eljárás egy tárolóhoz rendelt szerepkört rendel hozzá, de ugyanazokat a lépéseket követve rendelhet hozzá egy hatókörrel ellátott szerepkört egy várólistához: 

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a tárfiókot, és jelenítse meg a fiók **áttekintése.**
1. A Szolgáltatások csoportban válassza **a Blobok lehetőséget.** 
1. Keresse meg azt a tárolót, amelyhez szerepkört szeretne rendelni, és jelenítse meg a tároló beállításait. 
1. Válassza **a Hozzáférés-vezérlés (IAM)** lehetőséget a tároló hozzáférés-vezérlési beállításainak megjelenítéséhez. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.

    ![Képernyőkép a tárolóhozzáférés-vezérlési beállításokról](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Új szerepkör hozzáadásához kattintson a **Szerepkör-hozzárendelés hozzáadása** gombra.
1. A **Szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a hozzárendelni kívánt Azure Storage-szerepkört. Ezután keresse meg azt a rendszerbiztonsági tagot, amelyhez hozzá szeretné rendelni a szerepkört.

    ![RBAC szerepkör hozzárendelését bemutató képernyőkép](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kattintson a **Mentés** gombra. A szerepkörhöz rendelt identitás megjelenik a szerepkör alatt. Az alábbi képen például az látható, hogy a hozzáadott felhasználó olvasási engedéllyel rendelkezik a *mintatároló*nevű tárolóban lévő adatokhoz.

    ![Képernyőkép a szerepkörhöz rendelt felhasználók listájáról](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Hasonló lépéseket követve a tárfiókhoz, erőforráscsoporthoz vagy előfizetéshez hatókörrel ellátott szerepkör hozzárendeléséhez.

### <a name="assign-the-reader-role-for-portal-access"></a>Az Olvasó szerepkör hozzárendelése a portálhoz

Amikor egy beépített vagy egyéni szerepkört rendel az Azure Storage-hoz egy rendszerbiztonsági taghoz, engedélyeket ad a rendszerbiztonsági tagnak a tárfiókban lévő adatokon végzett műveletek végrehajtásához. A beépített **adatolvasó-szerepkörök** olvasási engedélyeket biztosítanak a tárolóban vagy várólistában lévő adatokhoz, míg a beépített **Data Contributor szerepkörök** olvasási, írási és törlési engedélyeket biztosítanak egy tárolóhoz vagy várólistahoz. Az engedélyek hatóköre a megadott erőforráshoz tartozik.  
Ha például a **Storage Blob Data Contributor** szerepkört egy **mintatároló**nevű tároló szintjén rendeli hozzá Mary felhasználóhoz, akkor Mária olvasási, írási és törlési hozzáférést kap a tárolóban lévő összes blobhoz.

Ha azonban Mária meg szeretne tekinteni egy blobot az Azure Portalon, akkor a **Storage Blob Data Contributor** szerepkör önmagában nem biztosít megfelelő engedélyeket a portálon való navigáláshoz a blobhoz annak megtekintéséhez. További Azure AD-engedélyek szükségesek a portálon való navigáláshoz és az ott látható egyéb erőforrások megtekintéséhez.

Ha a felhasználóknak hozzá kell tudniuk érni a blobokat az Azure Portalon, majd rendeljen hozzájuk egy további RBAC-szerepkört, az [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört ezekhez a felhasználókhoz, a tárfiók vagy a felett. Az **Olvasó** szerepkör egy Azure Resource Manager szerepkör, amely lehetővé teszi a felhasználók számára a tárfiók erőforrásainak megtekintését, de nem módosíthatja azokat. Nem biztosít olvasási engedélyeket az Azure Storage-ban lévő adatokhoz, hanem csak a fiókkezelési erőforrásokhoz.

Kövesse az alábbi lépéseket az **Olvasó** szerepkör hozzárendeléséhez, hogy a felhasználó hozzáférhessen a blobokhoz az Azure Portalról. Ebben a példában a hozzárendelés hatóköre a tárfiókhoz lesz:

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a tárfiókot.
1. Válassza a **Hozzáférés-vezérlés (IAM) lehetőséget** a tárfiók hozzáférés-vezérlési beállításainak megjelenítéséhez. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.
1. A **Szerepkör-hozzárendelés hozzáadása** ablakban válassza az **Olvasó** szerepkört. 
1. A **Hozzáférés hozzárendelése a** mezőben válassza az **Azure AD felhasználó, csoport vagy egyszerű szolgáltatás lehetőséget.**
1. Keressen meg azt a rendszerbiztonsági tagot, amelyhez a szerepkört hozzá kívánja rendelni.
1. Mentse a szerepkör-hozzárendelést.

**Az Olvasó** szerepkör hozzárendelése csak azon felhasználók számára szükséges, akiknek az Azure Portalhasználatával kell hozzáférnie a blobokhoz vagy várólistákhoz.

> [!IMPORTANT]
> A Storage Explorer előzetes verziója az Azure Portalon nem támogatja az Azure AD hitelesítő adatok megtekintéséhez és módosításához blob vagy várólista adatait. Az Azure Portalon található Storage Explorer mindig a fiókkulcsokat használja az adatok eléréséhez. A Storage Explorer azure-portálon való használatához hozzá kell rendelnie egy **microsoft.Storage/storageAccounts/listkeys/action szerepkört.**

## <a name="next-steps"></a>További lépések

- A tárolási erőforrások RBAC szerepköreiről az [Azure-blobokhoz és várólistákhoz való hozzáférés hitelesítése az Azure Active Directory használatával című](storage-auth-aad.md)témakörben talál további információt. 
- Ha többet szeretne megtudni az RBAC-ról, olvassa el [a Mi a szerepköralapú hozzáférés-vezérlés (RBAC)?](../../role-based-access-control/overview.md)
- Az RBAC-szerepkör-hozzárendelések Azure PowerShell, Az Azure CLI vagy a REST API használatával történő hozzárendelése és kezelése című témakörből megtudhatja:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure PowerShell segítségével](../../role-based-access-control/role-assignments-powershell.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API-val](../../role-based-access-control/role-assignments-rest.md)
- Ha meg szeretné tudni, hogyan engedélyezheti a tárolókhoz és várólistákhoz való hozzáférést a tárolóalkalmazásokból, olvassa [el az Azure AD használata az Azure Storage-alkalmazásokkal.](storage-auth-aad-app.md)
