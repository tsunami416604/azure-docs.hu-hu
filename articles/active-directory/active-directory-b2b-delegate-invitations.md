---
title: "Az Azure Active Directory B2B együttműködés meghívókat delegálása |} Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés felhasználó tulajdonságainak konfigurálható"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 78613cc978b585a98d235245194c02371f7f3849
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
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

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
