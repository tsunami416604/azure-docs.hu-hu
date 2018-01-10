---
title: "Adja hozzá, vagy az Azure Active Directory felhasználók törlése |} Microsoft Docs"
description: "Ismerteti, hogyan új felhasználók hozzáadása, vagy törölje a meglévő felhasználók az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: f0f18f377f194e78c05e63e9b6cbc31c1b945335
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Gyors üzembe helyezés: Új felhasználók hozzáadása az Azure Active Directoryban
Ez a cikk ismerteti a címkék törlése és felhasználók hozzáadása a szervezet az Azure portál használatával a orgnization Azure Active Directory (Azure AD) bérlői vagy szinkronizálása a helyszíni Windows Server AD felhasználói fiók adatait. 

## <a name="add-cloud-based-users"></a>Adja hozzá a felhőalapú felhasználók
1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **Azure Active Directory** , majd **felhasználók és csoportok**.
3. A **felhasználók és csoportok**, jelölje be **minden felhasználó**, majd válassza ki **új felhasználó**.
   ![Az Add parancs kiválasztása](./media/add-users-azure-active-directory/add-user.png)
4. Adja meg a felhasználó, például a **neve** és **felhasználónév**. A tartomány a felhasználónév részét kell lennie, a kezdeti alapértelmezett tartomány neve "[neve].onmicrosoft.com" vagy egy ellenőrzött és érvényes, nem összevont [egyéni tartománynév](add-custom-domain.md) például "contoso.com".
5. Másolja, vagy ellenkező esetben vegye figyelembe a létrehozott felhasználói jelszót, így megadhatja azt a felhasználó számára a folyamat befejezése után.
6. Másik lehetőségként megnyithatja és töltse ki **profil**, **csoportok**, vagy **Directory szerepkör** a felhasználó számára. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles-azure-portal.md).
7. A **felhasználói**, jelölje be **létrehozása**.
8. Az új felhasználó számára létrehozott jelszót biztonságos terjesztése, hogy a felhasználói bejelentkezés.

> [!TIP]
> Is szinkronizálhatja a helyszíni Windows Server AD a felhasználói fiók adatait. A Microsoft identity megoldások span a helyszíni és felhőalapú képességek, a hitelesítés és engedélyezés az összes erőforráshoz, függetlenül a hely egyetlen felhasználói azonosítót létrehozása. A hibrid identitás nevezzük. [Az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) a helyszíni címtárak integrálása az Azure Active Directory hibrid identitáskezelési forgatókönyvek esetén is használható. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. 

## <a name="delete-users-from-azure-ad"></a>Azure ad-felhasználók törlése
1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **felhasználók és csoportok**.
3. Az a **felhasználók és csoportok** panelen válassza ki a felhasználót, hogy törli a listáról. 
4. A kiválasztott felhasználó paneljén válassza **áttekintése**, majd a parancssávon válassza **törlése**.
   ![Az Add parancs kiválasztása](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Részletek 
* [Egy másik címtárból adja hozzá a vendégfelhasználók számára](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [A törölt felhasználói visszaállítása](active-directory-users-restore.md)



## <a name="next-steps"></a>További lépések
A gyors üzembe helyezés az új felhasználók felvétele az Azure AD Premium hogy megismerte. 

A következő hivatkozás használatával hozzon létre egy új felhasználót az Azure-portálon az Azure AD-ben.

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure AD-hez](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 