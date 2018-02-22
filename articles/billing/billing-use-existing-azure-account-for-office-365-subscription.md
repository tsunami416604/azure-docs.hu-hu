---
title: "Regisztráljon az Azure-fiók az Office 365 |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre az Office 365-előfizetéssel az Azure-fiók használatával"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: cjiang
ms.openlocfilehash: 8279bc2ba2032ee40d24ddb1f7f23e2725e56e7c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Regisztráljon az Azure-fiókjába az Office 365-előfizetéssel
Ha Ön Azure-előfizető, az Azure-fiók segítségével előfizetés az Office 365-előfizetéssel. Ha egy szervezet, amely rendelkezik az Azure-előfizetés részét, a felhasználók az Office 365-előfizetés a meglévő Azure Active Directoryban (Azure AD) is létrehozhat. Jelentkezzen egy olyan fiókkal, amely az Azure Active Directory-bérlő globális rendszergazdája és számlázási rendszergazda jogokkal rendelkezik Office 365. További információkért lásd: [ellenőrizze a fiók engedélyeit az Azure AD](#RoleInAzureAD) és [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

Ha már rendelkezik Office 365-fiókkal, mind az Azure-előfizetéssel, akkor [társítsa Azure-előfizetéshez az Office 365-bérlő](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Az Azure-fiók segítségével könnyebben nyerhet Office 365-előfizetéssel

1. Lépjen a [Office 365, termékoldala](https://products.office.com/business), és válassza ki a tervet.
2. Kattintson a **bejelentkezés** a lap jobb felső sarkában.

    ![Office 365 próba oldalát bemutató képernyőkép](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Jelentkezzen be Azure-fiók hitelesítő adataival. Ha a szervezete egy előfizetést hoz létre, használja az Azure-fiók, amely az az Azure Active Directory-bérlő globális rendszergazdája és számlázási rendszergazda szerepkör tagja.

    ![Képernyőkép az Office 365-bejelentkezés](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. Kattintson a **Kipróbálom most**.

    ![Képernyőkép a megerősíti, hogy az Office 365 rendelés.](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. A sorrend fogadását lapján kattintson a **Folytatás**.

    ![Az Office 365 rendelés fogadását képernyőképe](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

Most már készen is. Ha a szervezet hozott létre az Office 365-előfizetéssel, az alábbi lépések segítségével ellenőrizze, hogy az az Azure AD-felhasználók mostantól az Office 365-ben.

1. Nyissa meg az Office 365 felügyeleti központot.
2. Bontsa ki a **felhasználók**, és kattintson a **aktív felhasználók**.

    ![Az Office 365 felügyeleti központ felhasználók képernyőképe](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

A regisztrációt követően, az Office 365-előfizetéssel hozzáadódik az Azure Active Directory példányt, amely az Azure-előfizetéshez tartozik. További információkért lásd: [további információk az Office 365 és az Azure-előfizetések](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs) és [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Ellenőrizze a fiók engedélyeit az Azure ad-ben
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás**, majd keresse meg a **Active Directory**.

    ![Képernyőkép az Active Directory az Azure-portálon](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. Kattintson a **felhasználók és csoportok** > **minden felhasználó**.
4. Válassza ki a felhasználó nevét. 

    ![Képernyőkép az Azure Active Directory-felhasználók](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. Kattintson a **Directory szerepkör**.
  
    ![Az Azure portál directory szerepkör képernyőkép](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  A szerepkör **globális rendszergazda** vagy **korlátozott rendszergazdai** > **számlázási rendszergazda** a meglévő Azure Active Directory szolgáltatásban a felhasználók Office 365-előfizetéssel létrehozásához szükséges.

    ![Képernyőkép az Azure portál címtár szerepkörrel számlázási rendszergazda](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez. 
