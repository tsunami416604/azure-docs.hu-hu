---
title: Az Azure, Office 365-fiókjával regisztráljon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-előfizetés az Office 365-fiókja
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863440"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Egy Azure-előfizetést, az Office 365-fiókjával regisztráljon
Ha rendelkezik Office 365-előfizetéssel, az Office 365-fiók használatával Azure-előfizetés létrehozása. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használatával, az Office 365-felhasználónevét és jelszavát. Ha azt szeretné, állítsa be a virtuális gépek vagy más Azure-szolgáltatások használatára, jelentkezzen Azure-előfizetésre. Az Azure-előfizetés másokkal is megoszthat és [az Azure-előfizetés és erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Ha már rendelkezik Office 365-fiókja és a egy Azure-előfizetést, [egy Office 365-bérlő Azure-előfizetéshez társítja](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Office 365-fiók használata Azure-előfizetés beszerzése

Időt, és elkerülheti a számla elterjedése az Office 365-felhasználónevet és jelszót használó Azure-regisztráció. 

1. Regisztráció: [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Jelentkezzen be az Office 365-felhasználónevét és jelszavát. A használt fióknak nem szükséges rendszergazdai engedélyekkel kell rendelkeznie. Ha egynél több Office 365-fiókkal rendelkezik, győződjön meg a hitelesítő adatokat használja az Office 365-fiókkal, amelyet az Azure-előfizetés társítása szeretne-e. 

   ![Képernyőkép a bejelentkezési oldal.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Adja meg a szükséges adatokat, és a bejelentkezési folyamat elvégzése. Egyes adatokat nem lehet szükség, ha már rendelkezik Office 365-fiókkal.

    ![Képernyőkép a bejelentkezési űrlap.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Ha más személyek hozzáadása a szervezet az Azure-előfizetésre van szüksége, tekintse meg [hozzáférés-kezelés az Azure portal – első lépések](../role-based-access-control/overview.md). 

## <a id="more-about-subs">További információ az Azure és az Office 365-előfizetések</a>
Az Office 365 és az Azure-felhasználók és -előfizetések kezelése az Azure AD szolgáltatás használatával. Az Azure címtárban olyan, mintha egy tároló, amelyben csoportosíthatja a felhasználók és előfizetések. Ugyanazoknak a felhasználói fiókoknak az Office 365 és az Azure-előfizetésekre vonatkozó használatához győződjön meg arról, hogy az Azure-előfizetések jönnek létre az Office 365-előfizetések ugyanabban a címtárban kell. Vegye figyelembe a következőket:

* Egy könyvtár alatt előfizetés jön létre
* Felhasználók tartoznak a címtárakhoz
* Előfizetés hajtanak végre a felhasználó, akik az előfizetést hoz létre a címtárban. Így az Office 365-előfizetéssel, azzal a fiókkal, az Azure-előfizetésében vannak kötve.
* Az Azure-előfizetések a címtárban az egyes felhasználók tulajdonában
* Office 365-előfizetés tulajdonosa a könyvtár magát. A könyvtárból a megfelelő engedélyekkel rendelkező felhasználók kezelheti ezeket az előfizetéseket.

![Képernyőkép a kapcsolat a könyvtárat, a felhasználók és az előfizetések.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

További információkért lásd: [kapcsolódnak az Azure-előfizetések az Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).