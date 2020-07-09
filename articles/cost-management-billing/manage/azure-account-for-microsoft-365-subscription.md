---
title: Regisztrálás a Microsoft 365-be Azure-fiókkal
description: Tudnivalók Microsoft 365-előfizetés létrehozásáról egy Azure-fiók használatával
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: banders
ms.openlocfilehash: 006f8edc81ec19647ff6173a4c786c6d7e93fadc
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84810380"
---
# <a name="sign-up-for-a-microsoft--365-subscription-with-your-azure-account"></a>Regisztrálás Microsoft 365-előfizetésre Azure-fiókkal

Ha Ön Azure-előfizető, akkor Azure-fiókjával regisztrálhat Microsoft 365-előfizetésre. Ha Ön egy Azure-előfizetéssel rendelkező szervezet tagja, akkor a meglévő Azure Active Directory (Azure AD) felhasználói számára létrehozhat Microsoft 365-előfizetéseket. Olyan fiókkal regisztráljon a Microsoft 365-be, amely globális rendszergazdai vagy számlázási rendszergazdai engedélyekkel rendelkezik az Azure Active Directory-bérlőben. További információkért lásd [az Azure AD-ben a fiókengedélyek ellenőrzését](#RoleInAzureAD) és [az Azure Active Directoryban a rendszergazdai szerepkörök hozzárendelését](../../active-directory/users-groups-roles/directory-assign-admin-roles.md) ismertető cikket.

Ha már Microsoft 365-fiókkal és Azure-előfizetéssel is rendelkezik, [hozzárendelhet egy Microsoft 365-bérlőt az Azure-előfizetéshez](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Microsoft 365-előfizetés beszerzése az Azure-fiók használatával

1. Lépjen az [Microsoft 365 termékoldalára](https://www.microsoft.com/microsoft-365/business/all-business), és válasszon ki egy csomagot.
2. Az oldal jobb felső sarkában kattintson a **Bejelentkezés** lehetőségre.

    ![képernyőkép a Microsoft 365-próbaverzió oldaláról](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Jelentkezzen be Azure-fiókja hitelesítő adataival. Ha a szervezet számára hoz létre előfizetést, használjon egy olyan Azure-fiókot, amely a globális rendszergazdai vagy számlázási rendszergazdai címtárszerepkörrel rendelkezik az Azure Active Directory-bérlőben.

    ![Képernyőkép a Microsoft bejelentkezési felületéről](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Kattintson a **Kipróbálás** lehetőségre.

    ![Képernyőkép a Microsoft 365 megrendelésének megerősítéséről.](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. A rendelés visszaigazolási oldalán kattintson a **Folytatás** lehetőségre.

    ![A Microsoft 365 rendelési visszaigazolásának képernyőképe](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Ezzel készen is van. Ha létrehozta a Microsoft 365-előfizetést a szervezet számára, az alábbi lépésekkel ellenőrizheti, hogy az Azure AD-felhasználók már megtalálhatók-e a Microsoft 365-ben.

1. Nyissa meg a Microsoft 365 Felügyeleti központot.
2. Bontsa ki a **FELHASZNÁLÓK** területet, majd kattintson az **Aktív felhasználók** lehetőségre.

    ![Képernyőkép a Microsoft 365 Felügyeleti központ felhasználókat megjelenítő területéről](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

A regisztráció után a Microsoft 365-előfizetés ugyanahhoz az Azure Active Directory-példányhoz lesz hozzáadva, amelyhez az Azure-előfizetés tartozik. További információkért lásd [az Azure- és a Microsoft 365-előfizetésekkel kapcsolatos további tudnivalókat](microsoft-365-account-for-azure-subscription.md#more-about-subs) és [az Azure-előfizetések az Azure Active Directoryhoz társítását](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető cikket.

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Fiókengedélyek ellenőrzése az Azure AD-ben
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **Minden szolgáltatás** elemre, majd keresse meg az **Active Directoryt**.

    ![Képernyőkép az Active Directoryról az Azure Portalon](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Kattintson a **Felhasználók és csoportok** > **Minden felhasználó** lehetőségre.
4. Válassza ki a felhasználónevet.

    ![Képernyőkép az Azure Active Directory-felhasználókról](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Kattintson a **Címtárszerepkör** lehetőségre.

    ![Képernyőkép az Azure Portalon található címtárszerepkörről](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  A Microsoft 365-előfizetés meglévő Azure Active Directory-felhasználók számára történő létrehozásához a **Globális rendszergazda** vagy a **Korlátozott rendszergazda** > **Számlázási adminisztrátor** szerepkörre van szükség.

    ![Képernyőkép az Azure Portalon található Számlázási adminisztrátor címtárszerepkörről](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Microsoft 365-bérlő Azure-előfizetéshez történő hozzárendelése](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
