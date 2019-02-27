---
title: Klasszikus Azure-előfizetés rendszergazdái |} A Microsoft Docs
description: Az Azure társ-rendszergazdaként, és a szolgáltatás-rendszergazdai szerepkörök felvétele vagy módosítása, és a fiók rendszergazdája megtekintése ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a19153677e691d2681c131df67100a43ef9532a0
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889324"
---
# <a name="azure-classic-subscription-administrators"></a>Klasszikus Azure-előfizetés rendszergazdái

A Microsoft azt javasolja, hogy a szerepköralapú hozzáférés-vezérlés (RBAC) használata Azure-erőforrásokhoz való hozzáférés kezelésére. Azonban ha továbbra is a klasszikus üzemi modellt használ, szüksége lesz egy hagyományos előfizetés-rendszergazda szerepkör használata: Szolgáltatás-rendszergazda és a társ-rendszergazdaként. További információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

Ez a cikk bemutatja, hogyan lehet a társ-rendszergazdaként, és a szolgáltatás-rendszergazdai szerepkörök felvétele vagy módosítása, és a fiók rendszergazdája megtekintése.

## <a name="add-a-co-administrator"></a>Adja hozzá a társ-rendszergazda

> [!TIP]
> Csak kell hozzáadni a társ-rendszergazda, ha a felhasználónak az Azure klasszikus üzembe helyezés kezelése használatával [Azure Service Management PowerShell-modul](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure). Ha a felhasználó csak használ az Azure Portalon a klasszikus erőforrások felügyeletére, nem kell a felhasználó a hagyományos adminisztrátor hozzáadása.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a szolgáltatás-rendszergazdájaként.

1. Nyissa meg [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

    Társadminisztrátorok csak az előfizetések szintjén is hozzárendelhető.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **klasszikus rendszergazdák** fülre.

    ![Klasszikus rendszergazdák ablak képernyőképe](./media/classic-administrators/classic-administrators.png)

1. Kattintson a **Hozzáadás** > **adja hozzá a társ-rendszergazdaként** a Hozzáadás társadminisztrátorként panel megnyitásához.

    A Hozzáadás társadminisztrátorként beállítás le van tiltva, ha nem rendelkezik engedélyekkel.

1. Válassza ki a felhasználót, hogy adja hozzá, és kattintson a kívánt **Hozzáadás**.

    ![Képernyőkép, amely hozzáadja a társ-rendszergazdaként](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Vendégfelhasználó hozzáadása társadminisztrátorként

[Vendégfelhasználók](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) , amely a társ-rendszergazdaként hozzárendelt szerepkör láthat néhány eltérés, mint a korábban megszokott tagfelhasználó a társ-rendszergazda szerepkörrel. Vegye figyelembe az alábbi forgatókönyvet:

- Az Azure AD munkahelyi vagy iskolai fiókkal rendelkező felhasználó az Azure-előfizetés szolgáltatás-rendszergazdát.
- "B" felhasználó Microsoft-fiókkal rendelkezik.
- A felhasználó a Társadminisztrátori szerepkörhöz rendel a "b" felhasználónak
- "B" felhasználó szinte minden funkciója, de nem tudja keresése az Azure AD-címtárban található felhasználók vagy alkalmazások regisztrálását.

A "B" felhasználó sikerült mindent felügyelhetnek alakul. Ez a különbség oka, hogy a Microsoft-fiók hozzáadódik az előfizetés meg vendégként a tag felhasználó helyett. Vendégfelhasználók más alapértelmezett engedélyek rendelkezik, mint a korábban megszokott tagfelhasználó Azure AD-ben. Például tagfelhasználó más felhasználók olvashatja az Azure AD-ben, és a vendég felhasználók nem. Tag felhasználók regisztrálhatnak új szolgáltatásnevek Azure AD-ben, és a vendég felhasználók nem. Vendégfelhasználó képesnek kell lennie a következő feladatok végrehajtására, egy lehetséges megoldás-e az adott hozzárendelése az Azure AD-rendszergazdai szerepköröket a meghívott felhasználónak szüksége van. Például az előző forgatókönyvben, hozzárendelheti a [Címtárolvasók](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) szerepkör más felhasználók olvasása és hozzárendelése a [alkalmazásfejlesztő](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) hozhat létre egyszerű szolgáltatások szerepkört. Tagok és vendégfelhasználók és engedélyeiket kapcsolatos további információkért lásd: [Mik azok az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?](../active-directory/fundamentals/users-default-permissions.md).

Vegye figyelembe, hogy a [beépített szerepkörök az Azure-erőforrások](../role-based-access-control/built-in-roles.md) eltérnek a [az Azure AD-rendszergazdai szerepköröket](../active-directory/users-groups-roles/directory-assign-admin-roles.md). A beépített szerepkörök nem hozzáférést minden olyan Azure ad-hez. További információkért lásd: [megérteni a különféle szerepkörök](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Távolítsa el a társ-rendszergazda

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a szolgáltatás-rendszergazdájaként.

1. Nyissa meg [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

1. Kattintson a **hozzáférés-vezérlés (IAM)**.

1. Kattintson a **klasszikus rendszergazdák** fülre.

1. Adja hozzá a társ-rendszergazdaként eltávolítja melletti jelölőnégyzet bejelölésekor.

1. Kattintson a **eltávolítása**.

1. A megjelenő üzenetpanelen kattintson **Igen**.

    ![Képernyőkép, amely eltávolítja a társ-rendszergazdaként](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Megváltoztathatja a szolgáltatás-rendszergazdát.

Csak a fiók rendszergazdája módosíthatja az előfizetés szolgáltatás-rendszergazda. Alapértelmezés szerint az Azure-előfizetéssel, Regisztráljon a szolgáltatás-rendszergazda esetén ugyanaz, mint a fiók rendszergazdája. Nincs a felhasználóifiók-adminisztrátor szerepkörrel rendelkező felhasználó hozzáfér az Azure Portalon. A szolgáltatás-rendszergazdai szerepkörrel rendelkező felhasználó az Azure Portalra teljes hozzáféréssel rendelkezik. A felhasználóifiók-adminisztrátor és a szolgáltatás-rendszergazda nem ugyanaz a felhasználó és a egy másik felhasználó módosítja a szolgáltatás-rendszergazda, ha a fiók rendszergazdája elveszíti a hozzáférést az Azure Portalon. Azonban a fiók rendszergazdája mindig segítségével Fiókközpontban módosíthatja a szolgáltatás-rendszergazda vissza a saját magukat.

Módosítsa a szolgáltatás-rendszergazda kétféleképpen történhet. Módosíthatja a **az Azure portal** vagy **Account Center**.

### <a name="azure-portal"></a>Azure Portal

1. Ellenőrizze, hogy a forgatókönyv a szolgáltatás-rendszergazdák módosítása korlátozásai ellenőrzésével támogatott.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) fiók rendszergazdaként.

1. Nyissa meg [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

1. Kattintson a **Tulajdonságok** elemre.

    ![Képernyőfelvétel: a fiók rendszergazdája](./media/classic-administrators/account-admin.png)

1. A lap tetején kattintson **Szolgáltatásadminisztrátor** a szolgáltatás-rendszergazda panel megnyitásához.

    A szolgáltatás-rendszergazda gomb le van tiltva, ha nem rendelkezik engedélyekkel. Csak a felhasználót, aki a fiók rendszergazdája módosíthatja a szolgáltatás-rendszergazda.

1. Válassza ki az új szolgáltatás-rendszergazda, és kattintson a **mentése**.

### <a name="account-center"></a>Account Center webhelyen

1. Ellenőrizze, hogy a forgatókönyv a szolgáltatás-rendszergazdák módosítása korlátozásai ellenőrzésével támogatott.

1. Jelentkezzen be a [Fiókközpontban](https://account.windowsazure.com/subscriptions) fiók rendszergazdaként.

1. Kattintson egy előfizetésre.

1. Kattintson a jobb oldalon **előfizetés részleteinek szerkesztése**.

    ![Képernyőfelvétel: a Szerkesztés előfizetés gomb Account Center webhelyen](./media/classic-administrators/editsub.png)

1. Az a **szolgáltatás-rendszergazda** mezőbe írja be az e-mail-cím, az új szolgáltatás-rendszergazda.

    ![Képernyőfelvétel: a box, a szolgáltatás-rendszergazda e-mail-cím módosítása](./media/classic-administrators/change-service-admin.png)

1. Kattintson a pipára a módosítás mentéséhez.

### <a name="limitations-for-changing-the-service-administrator"></a>A szolgáltatás-rendszergazda módosítása vonatkozó korlátozások

Minden egyes előfizetés társítva az Azure AD-címtárral. A könyvtár, az előfizetés társítva, nyissa meg **előfizetések** a az Azure Portalon, majd tekintse meg a könyvtár-előfizetést.

Ha be van jelentkezve a munkahelyi vagy iskolai fiókkal jelentkezik be, a más fiókokat is hozzáadhat a szolgáltatás-rendszergazdaként a szervezetben. Például abby@contoso.com adhat hozzá bob@contoso.com , szolgáltatás-rendszergazda, de nem adható hozzá john@notcontoso.com , kivéve, ha john@notcontoso.com jelenlét rendelkezik a contoso.com címtárban. Felhasználó jelentkezett be a munkahelyi vagy iskolai fiókokhoz, szolgáltatás-rendszergazda felvétele a Microsoft-fiókok felhasználóinak továbbra is.

  | Bejelentkezési módszer | Adja hozzá a Microsoft-fiók felhasználói egy szolgáltatás-rendszergazdaként? | Adja hozzá munkahelyi vagy iskolai fiók ugyanazon a szervezeten belül egy szolgáltatás-rendszergazdaként? | Munkahelyi vagy iskolai fiók hozzáadása a más szervezetben egy szolgáltatás-rendszergazdaként? |
  | --- | --- | --- | --- |
  |  Microsoft-fiók |Igen |Nem |Nem |
  |  Munkahelyi vagy iskolai fiók |Igen |Igen |Nem |

## <a name="view-the-account-administrator"></a>A Fiókadminisztrátor megtekintéséhez

A fiók rendszergazdája a felhasználót, hogy kezdetben az Azure-előfizetést regisztrált, és felelős a számlázási tulajdonosa az előfizetés. Az előfizetés Fiókadminisztrátori módosításához lásd [Azure-előfizetés tulajdonjogának átruházása másik fiókra](../billing/billing-subscription-transfer.md).

Kövesse az alábbi lépéseket a fiók rendszergazdája megtekintéséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) , és válasszon ki egy előfizetést.

1. Kattintson a **Tulajdonságok** elemre.

    Az előfizetés Fiókadminisztrátori fiókjával jelenik meg a **Fiókadminisztrátor** mezőbe.

    ![Képernyőfelvétel: a fiók rendszergazdája](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>További lépések

* [A különböző Azure-beli szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Rbac-RÓL és az Azure portal segítségével Azure-erőforrásokhoz való hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md)
* [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md)
