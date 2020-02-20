---
title: Azure-regisztráció Office 365-fiókkal | Microsoft Docs
description: Tudnivalók Azure-előfizetés létrehozásáról egy Office 365-fiók használatával
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 265d9bcafa7caf9af0f8c4c7f33de044fa02349c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199585"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Regisztrálás Azure-előfizetésre Office 365-fiókkal
Ha rendelkezik Office 365-előfizetéssel, akkor Office 365-fiókja segítségével létrehozhat Azure-előfizetést. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) Office 365-felhasználónevével és -jelszavával. Ha virtuális gépeket szeretne beállítani, vagy más Azure-szolgáltatásokat szeretne használni, regisztrálnia kell egy Azure-előfizetést. Azure-előfizetését megoszthatja másokkal, és [szerepköralapú hozzáférés-vezérléssel kezelheti az Azure-előfizetéshez és -erőforrásokhoz való hozzáférést](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Ha már Office 365-fiókkal és Azure-előfizetéssel is rendelkezik, tekintse meg az [Office 365-bérlő Azure-előfizetéshez történő hozzárendelését](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető részt.

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Azure-előfizetés beszerzése az Office 365-fiók használatával

Időt takaríthat meg, és elkerülheti egy újabb fiók létrehozását, ha Office 365-felhasználónevével és -jelszavával regisztrál az Azure-ba.

1. Regisztráljon az [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs) webhelyen.
2. Jelentkezzen be Office 365-felhasználónevével és -jelszavával. A fióknak nem kell rendszergazdai engedélyekkel rendelkeznie. Ha több Office 365-fiókkal rendelkezik, ügyeljen arra, hogy annak az Office 365-fióknak a hitelesítő adatait használja, amelyet hozzá szeretne rendelni az Azure-előfizetéshez.

   ![Képernyőkép a bejelentkezési oldalról.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Adja meg a szükséges adatokat, és végezze el a bejelentkezési folyamatot. Előfordulhat, hogy bizonyos adatokra nem lesz szükség, ha már rendelkezik Office 365-fiókkal.

    ![Képernyőkép a regisztrációs űrlapról.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Ha a szervezetből további személyeket is szeretne hozzáadni az Azure-előfizetéshez, tekintse meg [a hozzáférés Azure Portalon történő kezelésének első lépéseit](../../role-based-access-control/overview.md) ismertető részt.

## <a id="more-about-subs">További információk az Azure- és az Office 365-előfizetésekről</a>
Az Office 365 és az Azure az Azure AD szolgáltatást használja a felhasználók és az előfizetések kezeléséhez. Az Azure-címtárak olyan tárolók, amelyekben csoportosíthatja a felhasználókat és az előfizetéseket. Ha ugyanazokat a felhasználói fiókokat szeretné használni az Azure- és az Office 365-előfizetésekhez, győződjön meg arról, hogy az Azure-előfizetések ugyanabban a címtárban lettek létrehozva, mint az Office 365-előfizetések. Tartsa szem előtt az alábbi szempontokat:

* Az előfizetések címtárakban jönnek létre.
* A felhasználók címtárakhoz tartoznak.
* Az előfizetések annak a felhasználónak a címtárába kerülnek, aki az előfizetést létrehozta. Tehát az Office 365-előfizetés ugyanahhoz a fiókhoz tartozik, mint az Azure-előfizetés.
* Az Azure-előfizetések tulajdonosai a címtár egyéni felhasználói.
* Az Office 365-előfizetések tulajdonosa maga a címtár. Az előfizetéseket a címtár megfelelő engedélyekkel rendelkező felhasználói kezelhetik.

![Képernyőkép a címtár, a felhasználók és az előfizetések kapcsolatáról.](./media/office-365-account-for-azure-subscription/19-background-information.png)

További információkért lásd [az Azure-előfizetések az Azure Active Directoryhoz társítását](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető cikket.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
