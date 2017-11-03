---
title: "Új felhasználók hozzáadása az Azure Active Directoryhoz | Microsoft Docs"
description: "Ismerteti, hogyan vehet fel új felhasználókat az Azure Active Directoryban."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 9b6a48220132bb8ea18ae5efca46ea2faf825806
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Gyors üzembe helyezés: Új felhasználók hozzáadása az Azure Active Directoryban
Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat a szervezet az Azure Active Directory (Azure AD) egyik az Azure portál használatával egyszerre, vagy a helyi Windows Server AD felhasználói fiók adatok szinkronizálása. 

## <a name="add-cloud-based-users"></a>Adja hozzá a felhőalapú felhasználók
1. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **Azure Active Directory** , majd **felhasználók és csoportok**.
3. A **felhasználók és csoportok**, jelölje be **minden felhasználó**, majd válassza ki **új felhasználó**.
   ![Az Add parancs kiválasztása](./media/add-users-azure-active-directory/add-user.png)
4. Adja meg a felhasználó, például a **neve** és **felhasználónév**. A tartomány a felhasználónév részét kell lennie, a kezdeti alapértelmezett tartomány neve "[neve].onmicrosoft.com" vagy egy ellenőrzött és érvényes, nem összevont [egyéni tartománynév](add-custom-domain.md) például "contoso.com".
5. Másolja, vagy ellenkező esetben vegye figyelembe a létrehozott felhasználói jelszót, így megadhatja azt a felhasználó számára a folyamat befejezése után.
6. Másik lehetőségként megnyithatja és töltse ki **profil**, **csoportok**, vagy **Directory szerepkör** a felhasználó számára. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles.md).
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
* [Külső felhasználó hozzáadásához](active-directory-users-create-external-azure-portal.md)

* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>Következő lépések
A gyors üzembe helyezés az új felhasználók felvétele az Azure AD Premium hogy megismerte. 

A következő hivatkozás használatával hozzon létre egy új felhasználót az Azure-portálon az Azure AD-ben.

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure AD-hez](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 