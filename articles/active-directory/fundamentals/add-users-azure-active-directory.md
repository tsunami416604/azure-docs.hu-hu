---
title: Felhasználók hozzáadása vagy törlése az Azure Active Directoryban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat, vagy hogyan törölhet meglévő felhasználókat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: e6e21ea09909a3bd92a21e15428af347e3f20b93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767392"
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>Rövid útmutató: Új felhasználók hozzáadása az Azure Active Directoryhoz
Ez a cikk azt ismerteti, hogyan törölhet vagy adhat hozzá felhasználókat cége Azure Active Directory-bérlőjéhez (Azure AD-bérlő) az Azure Portal használatával, illetve a saját helyszíni Windows Server AD felhasználói fiókadatainak szinkronizálásával. 

## <a name="add-cloud-based-users"></a>Felhőalapú felhasználók hozzáadása
1. Jelentkezzen be az [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Azure Active Directory**, majd a **Felhasználók és csoportok** elemet.
3. A **Felhasználók és csoportok** területen válassza a **Minden felhasználó**, majd az **Új felhasználó** lehetőséget.
   ![A Hozzáadás parancs kiválasztása](./media/add-users-azure-active-directory/add-user.png)
4. Adja meg a felhasználó adatait (például **Név** és **Felhasználónév**). A felhasználónév tartománynév részének a kezdeti alapértelmezett tartománynévnek („[tartománynév].onmicrosoft.com”), vagy egy ellenőrzött, nem összevont [egyéni tartománynévnek](add-custom-domain.md) kell lennie (pl. „contoso.com”).
5. Másolja a vágólapra vagy egyéb módon jegyezze fel a létrehozott jelszót, hogy megadhassa azt a felhasználónak a folyamat befejezése után.
6. Az adatokat a felhasználóhoz tartozó **Profil**, **Csoportok**, vagy **Címtárbeli szerepkör** területeken is megadhatja. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md).
7. A **Felhasználó** területen válassza a **Létrehozás** elemet.
8. Küldje el a létrehozott jelszót biztonságosan az új felhasználónak, hogy az be tudjon vele jelentkezni.

> [!TIP]
> Emellett a helyszíni Windows Server AD-ról is szinkronizálhatja a felhasználói adatokat. A Microsoft identitáskezelési megoldásai kiterjednek a helyszíni és felhőalapú képességekre is, így egyetlen felhasználói identitás jön létre hitelesítés és engedélyezés céljából az összes erőforráshoz, helytől függetlenül. Ezt hibrid identitásnak nevezzük. Az [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) használatával integrálhatja a helyszíni címtárakat az Azure Active Directoryval a hibrid identitáskezelési forgatókönyvek esetében. Így közös identitást biztosíthat a felhasználóinak az Azure AD-vel integrált Office 365-, Azure- és SaaS-alkalmazásokhoz. 

## <a name="delete-users-from-azure-ad"></a>Felhasználók törlése az Azure AD-ből
1. Jelentkezzen be az [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Felhasználók és csoportok** elemet.
3. A **Felhasználók és csoportok** panelen válassza ki a törölni kívánt felhasználót. 
4. A kiválasztott felhasználó paneljén válassza az **Áttekintés** elemet, majd a parancssávon válassza ki a **Törlés** parancsot.
   ![A Hozzáadás parancs kiválasztása](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>Részletek 
* [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md) 
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Törölt felhasználó visszaállítása](active-directory-users-restore.md)



## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban megismerhette, hogyan adhat hozzá új felhasználót az Azure AD Premiumhoz. 

Az alábbi hivatkozásra kattintva létrehozhat egy új felhasználót az Azure AD-ben az Azure Portal használatával.

>[!div class="nextstepaction"]
>[Felhasználók hozzáadása az Azure AD-hez](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)