---
title: Azure Active Directory B2B együttműködés felhasználók hozzáadása nélkül |} Microsoft Docs
description: Beállítható, hogy a Vendég felhasználó más vendég felhasználók hozzáadása az Azure ad az Azure Active Directory B2B együttműködés meghívót gyűjtésével nélkül.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5a37a5a14dcb07db7e078558072f7edad7432d9b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>Adja hozzá a B2B együttműködés vendégfelhasználók nélkül

> [!NOTE]
> Most vendégfelhasználók már nincs szüksége a meghívó e-mailek kivételével néhány bizonyos esetekben. További információkért lásd: [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md).  

Egy felhasználó, például a partner képviselője adhat hozzá felhasználókat a partnerről a szervezet felhívás váltható anélkül engedélyezheti. El kell végeznie jogosultságokat, hogy a felhasználó számbavételi használata a partnerrel. szervezeti a könyvtárban 

Megadja, hogy ezek a jogosultságok mikor:

1. A gazdagép szervezetében (például WoodGrove) a felhasználó meghívja a fiókpartner-szervezet egy felhasználót (például Sam@litware.com) vendégként.
2. A rendszergazda a gazdagép szervezet [állít be házirendek](active-directory-b2b-delegate-invitations.md) , amelyek lehetővé teszik a Sam azonosíthatja és más felhasználók felvétele az erőforráspartner-szervezet (Litware).
3. Most Sam származó is hozzáadhat más felhasználók Litware a WoodGrove könyvtár, csoportok vagy alkalmazások felhívás váltható anélkül. Ha a Sam Litware a számbavételi megfelelő jogosultságokkal rendelkezik, akkor automatikusan megtörténik.

### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
- [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
- [Az Azure Active Directory B2B együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)

