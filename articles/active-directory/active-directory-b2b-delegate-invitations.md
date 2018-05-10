---
title: Az Azure Active Directory B2B együttműködés meghívókat delegálása |} Microsoft Docs
description: Az Azure Active Directory B2B együttműködés felhasználó tulajdonságainak konfigurálható
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a4a1303c3992b8c576650859eea65591392b9b20
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="delegate-invitations-for-azure-active-directory-b2b-collaboration"></a>Az Azure Active Directory B2B együttműködés meghívókat delegálása

Az Azure Active Directory (Azure AD) üzleti vállalatközi (B2B) együttműködés nincs-e a globális rendszergazda az meghívókat. Ehelyett házirendekkel, és delegálja a felhasználók számára, akiknek szerepkörök engedélyezése meghívókat meghívókat. Új módja a Vendég felhasználói meghívókat delegálása van a Vendég meghívó szerepkörével.

## <a name="guest-inviter-role"></a>Vendég meghívó szerepkör
A felhasználó hozzárendelése azt a Vendég meghívó szerepkör meghívókat. Ne kelljen meghívókat küldhet a globális rendszergazdai szerepkör tagja lehet. Alapértelmezés szerint rendszeres felhasználók is hívhat meg a meghívás API egy globális rendszergazdai tiltja le a rendszeres felhasználók meghívókat. A felhasználó is hívhat meg az API-t az Azure-portálon vagy a PowerShell használatával.

Íme egy példa, amely bemutatja, hogyan lehet hozzáadni egy felhasználót a Vendég meghívó szerepkör a PowerShell használatával:

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="control-who-can-invite"></a>Személyek kérhetnek

![Szabályozza, hogyan hívhat meg](media/active-directory-b2b-delegate-invitations/control-who-to-invite.png)

Az Azure AD B2B együttműködés egy Bérlői rendszergazda állíthatja be a következő meghívó házirendek:

- Kapcsolja ki a meghívókat
- Csak rendszergazdák és a Vendég meghívó a szerepkörben levő felhasználók kérhetnek
- Rendszergazdák, a Vendég meghívó szerepkör és a tagjainak meghívása
- Minden felhasználó, beleértve a Vendégek, hívhat

Alapértelmezés szerint a bérlők #4 vannak állítva. (Az összes olyan felhasználót, beleértve a Vendégek, B2B felhasználók kérhetnek.)

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés, tekintse meg a következő cikkeket:

- [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Adja hozzá a B2B együttműködés vendégfelhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
- [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)


