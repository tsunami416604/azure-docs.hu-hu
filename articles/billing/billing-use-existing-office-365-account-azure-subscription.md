---
title: "Regisztráció az Azure Office 365-fiókkal |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure-előfizetés az Office 365-fiók használatával"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: cjiang
ms.openlocfilehash: 923b3ca8a2e2b1d7f44cd77946a02e1d30839f14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Előfizetés az Azure-előfizetéssel az Office 365-fiókkal
Ha olyan Office 365-előfizetéssel rendelkezik, az Office 365-fiók segítségével hozzon létre egy Azure-előfizetés. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) Office 365-felhasználónevét és jelszavát használva. Ha szeretné beállítani a virtuális gépek, vagy használjon más Azure-szolgáltatásokkal, kell regisztrál az Azure-előfizetésre. Az Azure-előfizetéshez megoszthatja másokkal és [az Azure-előfizetés és az erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlés használatával](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)

Ha már rendelkezik Office 365-fiókkal, mind az Azure-előfizetéssel, lásd: [társítsa Azure-előfizetéshez az Office 365-bérlő](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Az Office 365-fiókkal Azure-előfizetés igénylése

Időt takaríthat meg, és a fiók elterjedése elkerülheti, ha regisztrál az Azure-ban, az Office 365-felhasználónevét és jelszavát. 

1. Feliratkozhat [Azure.com webhelyre](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Jelentkezzen be az Office 365-felhasználónevét és jelszavát. A használt fióknak rendszergazdai jogosultságokkal rendelkezzenek nem szükséges. Ha egynél több Office 365-fiókra, győződjön meg arról, hogy a hitelesítő adatokat használja az Office 365-fiókra, amelyet szeretne társítani az Azure-előfizetéshez. 

   ![Képernyőkép a a bejelentkezési oldal.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Adja meg a szükséges adatokat, és a bejelentkezési folyamat elvégzése. Egyes adatokat nem lehet szükség, ha már rendelkezik Office 365-fiókkal.

    ![A bejelentkezési űrlap kitöltése képernyőkép.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Ha a szervezet az Azure-előfizetés hozzáadása a mások van szüksége, tekintse meg [Ismerkedés az Azure-portálon kezelési](../active-directory/role-based-access-control-what-is.md). 

## <a id="more-about-subs">További információ az Azure és az Office 365-előfizetések</a>
Az Office 365 és Azure az Azure AD szolgáltatás segítségével felhasználók és -előfizetések kezeléséhez. Az Azure-címtár olyan, mint amelyben csoportosíthatja a felhasználók és -előfizetések tárolója. Az Office 365 és az Azure-előfizetések ugyanazoknak a felhasználói fiókoknak használatához győződjön meg arról, hogy az Azure-előfizetések jöttek létre az Office 365-előfizetés ugyanabban a könyvtárban kell. Vegye figyelembe a következő szempontokat:

* Előfizetés végrehajtásakor létrejön egy könyvtár alá
* Felhasználók könyvtárak tartozik
* Előfizetés fájljai, a felhasználó az előfizetést hoz létre a könyvtár. Az Office 365-előfizetéssel, ugyanazt a fiókot, Azure-előfizetése van kötve.
* Azure-előfizetések a címtárban szereplő egyedi felhasználó tulajdonában
* Office 365-előfizetéssel a könyvtárat, maga a tulajdonosa. A könyvtárban lévő megfelelő jogosultságokkal rendelkező felhasználók kezelheti ezeket az előfizetéseket.

![Képernyőkép a kapcsolat a directory, a felhasználók és az előfizetések.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

További információkért lásd: [kapcsolódnak hogyan Azure-előfizetések az Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.
Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez. 
