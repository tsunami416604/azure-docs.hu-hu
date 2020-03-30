---
title: Klasszikus Azure-előfizetés-rendszergazdák | Microsoft dokumentumok
description: Ez a témakör az Azure társ-rendszergazdai és szolgáltatás-rendszergazdai szerepkörök hozzáadását és módosítását, valamint a fiókfelügyelő megtekintésének módját ismerteti.
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
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243483"
---
# <a name="azure-classic-subscription-administrators"></a>Klasszikus Azure-előfizetés-rendszergazdák

A Microsoft azt javasolja, hogy a szerepköralapú hozzáférés-vezérlés (RBAC) használatával kezelje az Azure-erőforrásokhoz való hozzáférést. Ha azonban továbbra is a klasszikus üzembe helyezési modellt használja, klasszikus előfizetés-rendszergazdai szerepkört kell használnia: szolgáltatás-rendszergazda és társrendszergazda. További információ: [Azure Resource Manager vs. classic deployment](../azure-resource-manager/management/deployment-models.md).

Ez a cikk a társrendszergazdai és szolgáltatás-rendszergazdai szerepkörök hozzáadását és módosítását, valamint a fiókadminisztrátor megtekintését ismerteti.

## <a name="add-a-co-administrator"></a>Társadminisztrátor hozzáadása

> [!TIP]
> Csak akkor kell társrendszergazdát hozzáadnia, ha a felhasználónak az Azure Service Management PowerShell-modul használatával kell kezelnie az [Azure Service Management PowerShell-modult.](https://docs.microsoft.com/powershell/module/servicemanagement/azure) Ha a felhasználó csak az Azure Portalon kezeli a klasszikus erőforrásokat, nem kell hozzáadnia a klasszikus rendszergazda a felhasználó számára.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként.

1. Nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nézetet, és válasszon ki egy előfizetést.

    A társrendszergazdák csak az előfizetéshatókörhöz rendelhetők hozzá.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Klasszikus rendszergazdák** fülre.

    ![Klasszikus rendszergazdákat megnyitó képernyőkép](./media/classic-administrators/classic-administrators.png)

1. Kattintson a**Társadminisztrátorhozzáadása** gombra a Társrendszergazdák hozzáadása ablaktábla megnyitásához. **Add** > 

    Ha a Társrendszergazda hozzáadása beállítás le van tiltva, nincs engedélye.

1. Jelölje ki a hozzáadni kívánt felhasználót, és kattintson a **Hozzáadás gombra.**

    ![Képernyőkép, amely társadminisztrátort ad hozzá](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Vendégfelhasználó hozzáadása társadminisztrátorként

Ha vendégfelhasználót szeretne társadminisztrátorként felvenni, kövesse ugyanazokat a lépéseket, mint az előző [Társrendszergazda hozzáadása](#add-a-co-administrator) szakaszban. A vendégfelhasználónak a következő feltételeknek kell megfelelnie:

- A vendégfelhasználónak jelen kell lennie a címtárban. Ez azt jelenti, hogy a felhasználó meghívást kapott a könyvtárba, és elfogadta a meghívást.

További információ avendégfelhasználó könyvtárhoz való hozzáadásáról az [Azure Active Directory B2B együttműködési felhasználóinak hozzáadása az Azure Portalon című témakörben található.](../active-directory/b2b/add-users-administrator.md)

### <a name="differences-for-guest-users"></a>Különbségek a vendégfelhasználók számára

A társrendszergazdaszerepkörrel rendelkező vendégfelhasználók bizonyos különbségeket láthatnak a társrendszergazdai szerepkörrel rendelkező tagfelhasználókhoz képest. Vegyük példaként a következő esetet:

- Az Azure AD-fiókkal (munkahelyi vagy iskolai fiókkal) rendelkező A felhasználó egy Azure-előfizetés szolgáltatás-rendszergazdája.
- "B" felhasználónak Microsoft-fiókja van.
- "A" felhasználó hozzárendeli a társrendszergazdai szerepkört a B felhasználóhoz.
- "B" felhasználó szinte mindent megtehet, de nem tud alkalmazásokat regisztrálni, vagy az Azure AD-címtárban felhasználókat keresni.

Azt várná, hogy a felhasználó B képes kezelni mindent. Ennek a különbségnek az az oka, hogy a Microsoft-fiók vendégfelhasználóként, nem pedig tagfelhasználóként kerül az előfizetésbe. A vendégfelhasználók az Azure AD-ben a tagfelhasználókhoz képest eltérő alapértelmezett engedélyekkel rendelkeznek. Például a tag felhasználók olvashatnak más felhasználók az Azure AD-ben, és a vendég felhasználók nem. A tagfelhasználók új egyszerű szolgáltatástagokat regisztrálhatnak az Azure AD-ben, a vendégfelhasználók pedig nem.

Ha egy vendégfelhasználónak képesnek kell lennie ezek végrehajtására, egy lehetséges megoldás az adott Azure AD-rendszergazdai szerepkörök hozzárendelése a vendégfelhasználó számára. Az előző esetben például hozzárendelheti a [Címtárolvasók](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) szerepkört más felhasználók olvasásához, és hozzárendelheti az [Alkalmazásfejlesztő](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) szerepkört, hogy egyszerű szolgáltatást hozhasson létre. A tagok és a vendégfelhasználókról és azok engedélyeiről a Következő témakörben olvashat: [Mik az alapértelmezett felhasználói engedélyek az Azure Active Directoryban?](../active-directory/fundamentals/users-default-permissions.md) A vendégfelhasználók hozzáférésének engedélyezéséről az [RBAC-ot használó külső vendégfelhasználók Azure-erőforrásokhoz való hozzáférésének kezelése](role-assignments-external-users.md)című témakörben talál további információt.

Vegye figyelembe, hogy az [Azure-erőforrások beépített szerepkörei](../role-based-access-control/built-in-roles.md) eltérnek az [Azure AD rendszergazdai szerepköreiétól.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) A beépített szerepkörök nem biztosítanak hozzáférést az Azure AD-hez. További információ: [A különböző szerepkörök ismertetése.](../role-based-access-control/rbac-and-directory-admin-roles.md)

A tag- és vendégfelhasználókat összehasonlító információk a [Mik az azure Active Directory alapértelmezett felhasználói engedélyei.](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Társadminisztrátor eltávolítása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként.

1. Nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nézetet, és válasszon ki egy előfizetést.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **Klasszikus rendszergazdák** fülre.

1. Jelölje be az eltávolítani kívánt társadminisztrátort.

1. Kattintson az **Eltávolítás** lehetőségre.

1. A megjelenő üzenetpanelen kattintson az **Igen**gombra.

    ![Képernyőkép, amely eltávolítja a társadminisztrátort](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Megváltoztathatja a szolgáltatás-rendszergazdát.

Csak a fiókfelügyelő módosíthatja az előfizetés szolgáltatásrendszergazdáját. Alapértelmezés szerint, ha regisztrál egy Azure-előfizetés, a szolgáltatás rendszergazdája ugyanaz, mint a fiók rendszergazda. A fiókfelügyelő szerepkörrel rendelkező felhasználó nem fér hozzá az Azure Portalhoz. A Szolgáltatásfelügyelő szerepkörrel rendelkező felhasználó teljes hozzáféréssel rendelkezik az Azure Portalhoz. Ha a fiókrendszergazda és a szolgáltatás-rendszergazda ugyanaz a felhasználó, és a szolgáltatás rendszergazdáját egy másik felhasználóra módosítja, akkor a fiókrendszergazda elveszíti az Azure Portalhoz való hozzáférést. A fiókfelügyelő azonban mindig használhatja a Fiókközpontot a szolgáltatásrendszergazdának saját magára való visszaváltásához.

Az alábbi lépésekkel módosíthatja a **Fiókközpont szolgáltatásrendszergazdáját.**

### <a name="account-center"></a>Fiókközpont

1. Győződjön meg arról, hogy a forgatókönyv támogatott a [Szolgáltatás-rendszergazda módosításának korlátainak](#limitations-for-changing-the-service-administrator)ellenőrzésével.

1. Jelentkezzen be az [Ügyfélközpontba](https://account.windowsazure.com/subscriptions) fiókadminisztrátorként.

1. Kattintson egy előfizetésre.

1. A jobb oldalon kattintson az **Előfizetés részleteinek szerkesztése gombra.**

    ![Képernyőkép az Előfizetés szerkesztése gombról az Ügyfélközpontban](./media/classic-administrators/editsub.png)

1. A **SERVICE ADMINISTRATOR** mezőbe írja be az új szolgáltatásadminisztrátor e-mail címét.

    ![Képernyőkép a szolgáltatásfelügyeleti e-mail módosításához](./media/classic-administrators/change-service-admin.png)

1. A módosítás mentéséhez kattintson a pipára.

### <a name="limitations-for-changing-the-service-administrator"></a>A szolgáltatásrendszergazdának a módosítására vonatkozó korlátozások

Azure-előfizetésenként csak egy szolgáltatás-rendszergazda lehet. A szolgáltatásrendszergazdának módosítása eltérően fog működni attól függően, hogy a fiókfelügyelő Microsoft-fiók-e, vagy Azure AD-fiókról (munkahelyi vagy iskolai fiókról) van-e szó.

| Fiókrendszergazda-fiók | Módosíthatja a szolgáltatásrendszergazdát egy másik Microsoft-fiókra? | Módosíthatja a szolgáltatás rendszergazdáját egy Azure AD-fiókra ugyanabban a címtárban? | Módosíthatja a szolgáltatás rendszergazdáját egy másik címtárban lévő Azure AD-fiókra? |
| --- | --- | --- | --- |
| Microsoft-fiók | Igen | Nem | Nem |
| Azure AD-fiók | Igen | Igen | Nem |

Ha a fiókrendszergazda egy Azure AD-fiók, módosíthatja a szolgáltatás rendszergazdája egy Azure AD-fiók ugyanabban a címtárban, de nem egy másik címtárban. Módosíthatja abby@contoso.com például a szolgáltatásrendszergazdát a bob@contoso.comlehetőségre, john@notcontoso.com john@notcontoso.com de a szolgáltatásrendszergazdát csak akkor módosíthatja, ha a contoso.com könyvtárban van.

A Microsoft-fiókokról és az Azure AD-fiókokról a [Mi az Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>A fiókadminisztrátor megtekintése

A fiókadminisztrátor az a felhasználó, aki eredetileg feliratkozott az Azure-előfizetésre, és az előfizetés számlázási tulajdonosaként felelős. Az előfizetés fiókadminisztrátorának módosításáról az [Azure-előfizetés tulajdonjogának átadása másik fiókba](../cost-management-billing/manage/billing-subscription-transfer.md)című témakörben jelenik meg.

A fiókadminisztrátor megtekintéséhez kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) nézetet, és válasszon ki egy előfizetést.

1. Kattintson a **Tulajdonságok** elemre.

    Az előfizetés fiókrendszergazdája megjelenik a **Fiókfelügyelő** mezőben.

    ![A fiókrendszergazdát ábrázoló képernyőkép](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>További lépések

* [A különböző Azure-beli szerepkörök ismertetése](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Azure-erőforrásokhoz való hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
* [Azure-előfizetés-rendszergazdák hozzáadása vagy módosítása](../cost-management-billing/manage/add-change-subscription-administrator.md)
