---
title: Office 365-höz készült Azure-fiókjával regisztráljon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Office 365-előfizetés az Azure-fiók használatával
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: ''
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 5ac05991bc79dcfd24a527a13084b3a9383b4839
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903987"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Office 365-előfizetéssel, az Azure-fiókkal regisztrálhat
Ha Ön Azure-előfizető, használhatja az Azure-fiókjába az Office 365-előfizetéssel regisztrálhat. Ha Ön Azure-előfizetéssel rendelkező egy szervezet része, a felhasználók számára az Office 365-előfizetés a meglévő Azure Active Directoryban (Azure AD) is létrehozhat. Iratkozzon fel az Office 365-fiókkal, amely az Azure Active Directory-bérlő globális rendszergazdai vagy számlázási rendszergazdájának-engedélyekkel rendelkezik. További információkért lásd: [ellenőrizze a saját fiók engedélyeit az Azure ad-ben](#RoleInAzureAD) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Ha már rendelkezik Office 365-fiókja és a egy Azure-előfizetést, akkor [egy Office 365-bérlő Azure-előfizetéshez társítja](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Office 365-előfizetés beszerzése az Azure-fiók használatával

1. Nyissa meg a [Office 365 termékoldalán](https://products.office.com/business), és válassza ki azt a csomagot.
2. Kattintson a **jelentkezzen be a** az oldal jobb felső sarkában.

    ![Office 365-próbaverziós oldalát bemutató képernyőkép](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Jelentkezzen be Azure-fiók hitelesítő adataival. Előfizetés létrehozásánál szervezete számára, használja az Azure-fiókkal, amely az Azure Active Directory-bérlő globális rendszergazdai vagy számlázási rendszergazdájának címtárbeli szerepkör tagja.

    ![Képernyőkép az Office 365-bejelentkezés](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Kattintson a **kipróbálása**.

    ![Képernyőfelvétel:, amely megerősíti, hogy a rendelés az Office 365-höz.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. Kattintson a rendelés visszaigazolási lapja **Folytatás**.

    ![Az Office 365 rendelési visszaigazoláson képernyőképe](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Most már készen vagyunk. Ha a szervezet számára létrehozott az Office 365-előfizetéssel, a következő lépések használatával ellenőrizze, hogy az Azure AD-felhasználók mostantól az Office 365-ben.

1. Nyissa meg az Office 365 felügyeleti központban.
2. Bontsa ki a **felhasználók**, és kattintson a **aktív felhasználók**.

    ![Képernyőkép a Office 365 felügyeleti központ](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

Miután regisztrált, az Office 365-előfizetés Azure Active Directory ugyanazon az Azure-előfizetéshez tartozó kerül. További információkért lásd: [Azure és az Office 365 előfizetési lehetőségekről további](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) és [kapcsolódnak az Azure-előfizetések az Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Ellenőrizze a saját fiók engedélyeit az Azure ad-ben
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás**, és keressen **Active Directory**.

    ![Képernyőfelvétel: az Active Directory az Azure Portalon](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Kattintson a **felhasználók és csoportok** > **minden felhasználó**.
4. Válassza ki a felhasználó nevét. 

    ![Képernyőkép az Azure Active Directory-felhasználók](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Kattintson a **címtárbeli szerepkör**.
  
    ![Képernyőkép az Azure portal címtárbeli szerepkört](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  A szerepkör **globális rendszergazdai** vagy **korlátozott rendszergazda** > **számlázási rendszergazda** kell létrehoznia az Office 365-előfizetéssel, a felhasználók számára a meglévő, az Azure Active Directory.

    ![Képernyőkép az Azure portal címtárszerepkör számlázási rendszergazda](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458). 
