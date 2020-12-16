---
title: Válassza ki, hogyan engedélyezze a hozzáférést az üzenetsor-információhoz a Azure Portal
titleSuffix: Azure Storage
description: Amikor a Azure Portal használatával fér hozzá a várólista adataihoz, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba érkező kérések hitelesítése és engedélyezése az Azure AD-fiók vagy a Storage-fiók elérési kulcsa alapján lehetséges.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozguns
ms.date: 09/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: contperf-fy21q1
ms.openlocfilehash: 504d2eb939758e6045a2af095c66093c8754cb94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590749"
---
# <a name="choose-how-to-authorize-access-to-queue-data-in-the-azure-portal"></a>Válassza ki, hogyan engedélyezze a hozzáférést az üzenetsor-információhoz a Azure Portal

Amikor a [Azure Portal](https://portal.azure.com)használatával fér hozzá a várólista adataihoz, a portálon az Azure Storage-ba irányuló kérések is elérhetők. Az Azure Storage-ba irányuló kérelem az Azure AD-fiók vagy a Storage-fiók hozzáférési kulcsa alapján engedélyezhető. A portál jelzi, hogy melyik módszert használja, és lehetővé teszi a kettő közötti váltást, ha rendelkezik a megfelelő engedélyekkel.

## <a name="permissions-needed-to-access-queue-data"></a>A várólista-adateléréshez szükséges engedélyek

Attól függően, hogy hogyan kívánja engedélyezni az üzenetsor-azonosítók elérését a Azure Portalban, egyedi engedélyekre van szüksége. A legtöbb esetben ezek az engedélyek az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával érhetők el. További információ az Azure RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).

### <a name="use-the-account-access-key"></a>A fiók elérési kulcsának használata

A várólista-információk fiók-hozzáférési kulccsal való eléréséhez hozzá kell rendelnie egy Azure-szerepkört, amely tartalmazza az Azure RBAC műveletet `Microsoft.Storage/storageAccounts/listkeys/action` . Ez az Azure-szerepkör beépített vagy egyéni szerepkör lehet. `Microsoft.Storage/storageAccounts/listkeys/action`Többek között a következőket támogató beépített szerepkörök:

- A Azure Resource Manager [tulajdonosi szerepkör](../../role-based-access-control/built-in-roles.md#owner)
- A Azure Resource Manager [közreműködő szerepkör](../../role-based-access-control/built-in-roles.md#contributor)
- A [Storage-fiók közreműködői szerepköre](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Amikor megpróbál hozzáférni a várólista-információhoz a Azure Portalban, a portál először ellenőrzi, hogy van-e hozzárendelve szerepkör a következővel: `Microsoft.Storage/storageAccounts/listkeys/action` . Ha ezzel a művelettel társított egy szerepkört, akkor a portál a fiók kulcsát használja a várólista-adatok eléréséhez. Ha nem rendelt hozzá szerepkört ezzel a művelettel, akkor a portál az Azure AD-fiókjával próbál hozzáférni az adataihoz.

> [!NOTE]
> A klasszikus előfizetés-rendszergazdai szerepkörök **szolgáltatás rendszergazdája** és a **társ-rendszergazda** a Azure Resource Manager szerepkör megfelelőjét tartalmazza [`Owner`](../../role-based-access-control/built-in-roles.md#owner) . A **tulajdonosi** szerepkör magában foglalja az összes műveletet, beleértve a `Microsoft.Storage/storageAccounts/listkeys/action` -t is, így az egyik rendszergazdai szerepkörrel rendelkező felhasználó is elérheti a várólista-adataikat a fiók kulcsával. További információ: [klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure ad-rendszergazdai szerepkörök](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="use-your-azure-ad-account"></a>Az Azure AD-fiók használata

Ha az Azure AD-fiók használatával szeretné elérni a Azure Portal üzenetsor-adatait, az alábbi utasítások mindegyikének igaznak kell lennie az Ön számára:

- A Azure Resource Manager szerepkört legalább a [`Reader`](../../role-based-access-control/built-in-roles.md#reader) Storage-fiók szintjére vagy magasabbra kell rendelni. Az **olvasó** szerepkör biztosítja a legtöbb korlátozott engedélyt, de egy másik Azure Resource Manager szerepkör is elfogadható, amely hozzáférést biztosít a Storage-fiókok felügyeleti erőforrásaihoz.
- Olyan beépített vagy egyéni szerepkört rendelt hozzá, amely hozzáférést biztosít a várólista-adathoz.

Az **olvasó** szerepkör-hozzárendelés vagy egy másik Azure Resource Manager szerepkör-hozzárendelés szükséges ahhoz, hogy a felhasználó megtekinthesse és navigáljon a tárolási fiókok felügyeleti erőforrásaihoz a Azure Portal. Azok az Azure-szerepkörök, amelyek hozzáférést biztosítanak a várólista-adatkészletekhez, nem biztosítanak hozzáférést a Storage-fiókok felügyeleti erőforrásaihoz. A várólista-információk a portálon való eléréséhez a felhasználónak engedélyekre van szüksége a Storage-fiók erőforrásainak megkereséséhez. További információ erről a követelményről: [az olvasó szerepkör kiosztása a portálhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

A várólista-adataihoz való hozzáférést támogató beépített szerepkörök a következők:

- [Tárolási várólista adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): olvasási/írási/törlési engedélyek a várólistákhoz.
- [Tárolási várólista Adatolvasója](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): írásvédett engedélyek a várólistákhoz.

Az egyéni szerepkörök a beépített szerepkörök által biztosított azonos engedélyek különböző kombinációit támogatják. Az egyéni Azure-szerepkörök létrehozásával kapcsolatos további információkért lásd: Azure-beli [Egyéni szerepkörök](../../role-based-access-control/custom-roles.md) és [a szerepkör-definíciók ismertetése az Azure-erőforrásokhoz](../../role-based-access-control/role-definitions.md).

A klasszikus előfizetés-rendszergazdai szerepkörrel rendelkező várólisták listázása nem támogatott. A várólisták listázásához a felhasználónak hozzá kell rendelnie a Azure Resource Manager **olvasó** szerepkört, a **tárolási üzenetsor Adatolvasó** szerepkörét vagy a **tárolási várólista adatközreműködői** szerepkörét.

> [!IMPORTANT]
> A Azure Portal Storage Explorer előzetes verziója nem támogatja az Azure AD-beli hitelesítő adatok használatát a várólista-adatok megtekintéséhez és módosításához. Storage Explorer a Azure Portal mindig a fiók kulcsait használja az adateléréshez. A Azure Portal Storage Explorer használatához hozzá kell rendelnie egy olyan szerepkört, amely tartalmazza a következőt: `Microsoft.Storage/storageAccounts/listkeys/action` .

## <a name="navigate-to-queues-in-the-azure-portal"></a>Navigáljon a Azure Portalban található várólistákhoz

A várólista-információk a portálon való megtekintéséhez navigáljon a Storage-fiók **áttekintéséhez** , és kattintson a **várólistákra** mutató hivatkozásokra. Másik lehetőségként megnyithatja a menü **Queue szolgáltatás** szakaszát.

:::image type="content" source="media/authorize-data-operations-portal/queue-access-portal.png" alt-text="Képernyőfelvétel az üzenetsor-adatAzure Portal":::

## <a name="determine-the-current-authentication-method"></a>Az aktuális hitelesítési módszer meghatározása

Amikor egy várólistára navigál, a Azure Portal jelzi, hogy jelenleg a fiók-hozzáférési kulcsot vagy az Azure AD-fiókot használja a hitelesítéshez.

### <a name="authenticate-with-the-account-access-key"></a>Hitelesítés a fiók hozzáférési kulcsával

Ha a fiók-hozzáférési kulccsal végzi a hitelesítést, a hitelesítési módszerként megadott **elérési kulcsot** fogja látni a portálon:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="Képernyőfelvétel, amely az aktuálisan hozzáférő várólistákat jeleníti meg a fiók kulcsával":::

Ha az Azure AD-fiók használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik a megfelelő engedélyekkel az Ön által hozzárendelt Azure-szerepkörökön keresztül, folytathatja a folytatást. Ha azonban nem rendelkezik a megfelelő engedélyekkel, a következőhöz hasonló hibaüzenet jelenik meg:

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Hiba jelenik meg, ha az Azure AD-fiók nem támogatja a hozzáférést":::

Figyelje meg, hogy ha az Azure AD-fiókja nem rendelkezik a megtekintéshez szükséges engedélyekkel, a listában nem jelenik meg a várólisták. Kattintson a **kapcsolóra** a Hívóbetű eléréséhez, és használja a hozzáférési kulcsot a hitelesítéshez.

### <a name="authenticate-with-your-azure-ad-account"></a>Hitelesítés az Azure AD-fiókkal

Ha az Azure AD-fiókkal végzi a hitelesítést, akkor a portálon hitelesítési módszerként megadott **Azure ad-felhasználói fiókot** fogja látni:

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Az Azure AD-fiókkal aktuálisan hozzáférő várólistákat megjelenítő felhasználó képernyőképe":::

Ha a fiók-hozzáférési kulcs használatára szeretne váltani, kattintson a képre Kiemelt hivatkozásra. Ha rendelkezik hozzáféréssel a fiók kulcsához, akkor folytathatja a folytatást. Ha azonban nincs hozzáférése a fiók kulcsához, a Azure Portal hibaüzenetet jelenít meg.

A várólisták nem jelennek meg a portálon, ha nem rendelkezik hozzáféréssel a fiók kulcsaihoz. Kattintson a **Váltás az Azure ad felhasználói fiókra** hivatkozásra az Azure ad-fiók ismételt hitelesítésre való használatához.

## <a name="next-steps"></a>Következő lépések

- [Azure-blobok és-várólisták hozzáférésének hitelesítése Azure Active Directory használatával](../common/storage-auth-aad.md)
- [A Azure Portal használata Azure-szerepkörök hozzárendeléséhez a blob-és üzenetsor-adat eléréséhez](../common/storage-auth-aad-rbac-portal.md)
- [Az Azure CLI-vel hozzárendelhet egy Azure-szerepkört a blob-és üzenetsor-adat eléréséhez](../common/storage-auth-aad-rbac-cli.md)
- [A Azure PowerShell modul használatával rendeljen hozzá egy Azure-szerepkört a blob-és üzenetsor-információ eléréséhez](../common/storage-auth-aad-rbac-powershell.md)
