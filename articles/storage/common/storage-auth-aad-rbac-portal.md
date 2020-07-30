---
title: A Azure Portal használata RBAC-szerepkör hozzárendeléséhez adathozzáféréshez
titleSuffix: Azure Storage
description: Megtudhatja, hogyan rendelhet hozzá engedélyeket egy Azure Active Directory rendszerbiztonsági tag számára az Azure Portal segítségével szerepköralapú hozzáférés-vezérléssel (RBAC). Az Azure Storage támogatja az Azure AD-n keresztül történő hitelesítéshez használható beépített és egyéni Azure-szerepköröket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 28cb140293d449876cc05e3e5f9e59a04b8396e0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423710"
---
# <a name="use-the-azure-portal-to-assign-an-rbac-role-for-access-to-blob-and-queue-data"></a>Az Azure Portal használata a blob- és üzenetsoradatok elérésére szolgáló RBAC-szerepkör hozzárendeléséhez

Azure Active Directory (Azure AD) a [szerepköralapú hozzáférés-vezérlés (RBAC)](../../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage egy beépített Azure-beli szerepkört határoz meg, amely magában foglalja a blob-vagy üzenetsor-adateléréshez használt engedélyek közös készleteit.

Ha egy Azure AD-rendszerbiztonsági tag egy RBAC-szerepkört rendel hozzá, az Azure hozzáférést biztosít ezen rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Storage-fiók vagy egy adott tároló vagy várólista szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával hozzárendelni a RBAC-szerepköröket. A Azure Portal egyszerű felületet biztosít a RBAC-szerepkörök hozzárendeléséhez és a tárolási erőforrásokhoz való hozzáférés kezeléséhez. Az Azure parancssori eszközei vagy az Azure Storage Management API-k segítségével RBAC-szerepköröket is hozzárendelhet a blob-és üzenetsor-erőforrásokhoz. További információ a tárolási erőforrások RBAC szerepköreiről: az [Azure-blobok és-várólisták hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md).

## <a name="rbac-roles-for-blobs-and-queues"></a>Blobok és várólisták RBAC szerepkörei

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás hatókörének meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>RBAC szerepkörök kiosztása a Azure Portal használatával

Miután meghatározta a szerepkör-hozzárendelés megfelelő hatókörét, navigáljon az adott erőforráshoz a Azure Portal. Jelenítse meg az erőforrás **Access Control (iam)** beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

1. Rendelje hozzá a megfelelő Azure Storage RBAC-szerepkört, hogy hozzáférést biztosítson egy Azure AD rendszerbiztonsági tag számára.

1. Rendelje hozzá a Azure Resource Manager- [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört azokhoz a felhasználókhoz, akiknek Azure Portal az Azure ad-beli hitelesítő adataik használatával hozzá kell férniük a tárolók vagy várólistákhoz 

Az alábbi szakaszok részletesebben ismertetik ezeket a lépéseket.

> [!NOTE]
> Az Azure Storage-fiók tulajdonosaként nincs automatikusan hozzárendelve az adathozzáféréshez szükséges engedélyek. Explicit módon hozzá kell rendelnie egy RBAC-szerepkört az Azure Storage-hoz. Az előfizetés, az erőforráscsoport, a Storage-fiók vagy egy tároló vagy várólista szintjén rendelhető hozzá.
>
> Nem rendelhet hozzá hatókörhöz tartozó szerepkört egy tárolóhoz vagy várólistához, ha a Storage-fiókhoz engedélyezve van egy hierarchikus névtér.

### <a name="assign-an-azure-built-in-role"></a>Azure beépített szerepkörének kiosztása

Mielőtt szerepkört rendeljen egy rendszerbiztonsági tag számára, vegye figyelembe a megadható engedélyek hatókörét. Tekintse át az [erőforrás-hatókör meghatározása](#determine-resource-scope) szakaszt a megfelelő hatókör eldöntéséhez.

Az itt bemutatott eljárás egy tárolóra vonatkozó hatókört rendel hozzá, de ugyanezen lépések végrehajtásával hozzárendelheti az üzenetsor hatókörét:

1. A [Azure Portal](https://portal.azure.com)nyissa meg a Storage-fiókját, és jelenítse meg a fiók **áttekintését** .
1. A szolgáltatások területen válassza a **Blobok**elemet.
1. Keresse meg azt a tárolót, amelyhez hozzá szeretné rendelni a szerepkört, és jelenítse meg a tároló beállításait.
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a tároló hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.

    ![A tároló hozzáférés-vezérlési beállításait bemutató képernyőfelvétel](media/storage-auth-aad-rbac-portal/portal-access-control-for-storage.png)

1. Új szerepkör hozzáadásához kattintson a **szerepkör-hozzárendelés hozzáadása** gombra.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a hozzárendelni kívánt Azure Storage-szerepkört. Ezután keresse meg a rendszerbiztonsági tag, amelyhez hozzá szeretné rendelni a szerepkört.

    ![A RBAC-szerepkör hozzárendelését bemutató képernyőkép](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Kattintson a **Mentés** gombra. Az az identitás, akihez a szerepkört hozzárendelte, megjelenik az adott szerepkör alatt. Az alábbi képen például látható, hogy a felhasználó hozzáadta a *minta-tároló*nevű tárolóban lévő adatok olvasási engedélyét.

    ![A szerepkörhöz hozzárendelt felhasználók listáját megjelenítő képernyőkép](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

A hasonló lépéseket követve rendelhet hozzá egy szerepkör hatókörét a Storage-fiókhoz, az erőforráscsoporthoz vagy az előfizetéshez.

### <a name="assign-the-reader-role-for-portal-access"></a>Az olvasó szerepkör kiosztása a portálhoz való hozzáféréshez

Ha beépített vagy egyéni szerepkört rendel az Azure Storage-hoz egy rendszerbiztonsági tag számára, engedélyeket ad az adott rendszerbiztonsági tag számára a Storage-fiókban lévő adatokkal kapcsolatos műveletek elvégzéséhez. A beépített **Adatolvasó** szerepkörök olvasási engedélyeket biztosítanak egy tárolóban vagy várólistában lévő adathoz, míg a beépített **adatközreműködői** szerepkörök olvasási, írási és törlési engedélyeket biztosítanak egy tárolóhoz vagy várólistához. Az engedélyek hatóköre a megadott erőforrásra terjed ki.  
Ha például a **Storage blob-adatközreműködői** szerepkört egy **minta-tároló**nevű tároló szintjén rendeli a felhasználó Mary-hez, akkor a Mary a tároló összes blobjának olvasási, írási és törlési jogosultságot kap.

Ha azonban Mária szeretné megtekinteni a blobot a Azure Portalban, akkor a **Storage blob-adatközreműködői** szerepkör önmagában nem biztosít megfelelő engedélyeket a portálon a blobhoz való átjáráshoz a megtekintéshez. További Azure AD-engedélyek szükségesek a portálon való böngészéshez és az ott látható egyéb erőforrások megtekintéséhez.

Ha a felhasználóknak el kell tudniuk érni a blobokat a Azure Portalban, majd egy további RBAC-szerepkört, az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört kell hozzárendelni a felhasználóknak a Storage-fiók vagy a fenti szint szintjén. Az **olvasó** szerepkör egy Azure Resource Manager szerepkör, amely lehetővé teszi a felhasználóknak a Storage-fiók erőforrásainak megtekintését, de nem módosíthatják azokat. Nem biztosít olvasási engedélyeket az Azure Storage-beli adattároláshoz, de csak a fiókok felügyeleti erőforrásaihoz.

Kövesse az alábbi lépéseket az **olvasó** szerepkör hozzárendeléséhez, hogy egy felhasználó hozzáférhessen a blobokhoz a Azure Portal. Ebben a példában a hozzárendelés a Storage-fiókra terjed ki:

1. Az [Azure Portalon](https://portal.azure.com) lépjen a tárfiókra.
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a Storage-fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki az **olvasó** szerepkört. 
1. A **hozzáférés kiosztása** mezőben válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**elemet.
1. Keresse meg a rendszerbiztonsági tag, amelyhez hozzá szeretné rendelni a szerepkört.
1. Mentse a szerepkör-hozzárendelést.

Az **olvasó** szerepkör kiosztása csak olyan felhasználók számára szükséges, akiknek a Azure Portal használatával kell a blobokhoz vagy várólistákhoz hozzáférni.

> [!IMPORTANT]
> A Azure Portal Storage Explorer előzetes verziója nem támogatja az Azure AD-beli hitelesítő adatok használatát a blob-vagy üzenetsor-adatok megtekintésére és módosítására. Storage Explorer a Azure Portal mindig a fiók kulcsait használja az adateléréshez. A Azure Portal Storage Explorer használatához olyan szerepkört kell hozzárendelni, amely tartalmazza a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**.

## <a name="next-steps"></a>Következő lépések

- További információ a tárolási erőforrások RBAC szerepköreiről: az [Azure-blobok és-várólisták hozzáférésének hitelesítése Azure Active Directory használatával](storage-auth-aad.md). 
- További információ a RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).
- Az Azure-beli szerepkör-hozzárendelések Azure PowerShell, az Azure CLI vagy a REST API használatával történő hozzárendeléséről és kezeléséről a következő cikkekben talál további információt:
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése az Azure CLI-vel](../../role-based-access-control/role-assignments-cli.md)
    - [Szerepköralapú hozzáférés-vezérlés (RBAC) kezelése a REST API](../../role-based-access-control/role-assignments-rest.md)
- Ha szeretné megtudni, hogyan engedélyezheti a tárolók és a várólisták hozzáférését a Storage-alkalmazásokban, tekintse meg az Azure [ad és az Azure Storage alkalmazások használatát](storage-auth-aad-app.md)ismertető témakört.
