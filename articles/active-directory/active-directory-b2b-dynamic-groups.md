---
title: Dinamikus csoportok és az Azure Active Directory B2B együttműködés |} Microsoft Docs
description: Bemutatja, hogyan használható az Azure AD dinamikus csoportok Azure Active Directory B2B együttműködés
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 751502c2be84e9454c507f09a47b609d003ce2c5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dinamikus csoportok és az Azure Active Directory B2B együttműködés

## <a name="what-are-dynamic-groups"></a>Mik azok a dinamikus csoportok?
Az Azure Active Directory (Azure AD), biztonsági csoporttagság dinamikus beállítási lehetőségek érhetők el a [az Azure-portálon](https://portal.azure.com). A rendszergazdák az Azure AD felhasználói attribútumok (például userType, részleg vagy ország) alapján létrehozott csoportok feltöltéséhez szabályainak beállítása. Tagok automatikusan lehet hozzáadni vagy az attribútum a biztonsági csoportból eltávolított. Ezek a csoportok-alkalmazásokra vagy a felhőben található erőforrásokat (SharePoint-webhelyekhez, dokumentumok) és licencek hozzárendelése tagok hozzáférést biztosíthat. További információk a dinamikus csoportok [dedikált csoportok az Azure Active Directoryban](active-directory-accessmanagement-dedicated-groups.md).

A megfelelő [Azure AD Premium P1 és P2 licencelési](https://azure.microsoft.com/pricing/details/active-directory/) létrehozása és dinamikus csoportok használata szükséges. További információ: a cikk [dinamikus csoporttagság Attribútumalapú szabályok létrehozása az Azure Active Directoryban](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Mik a beépített dinamikus csoportot?
A **minden felhasználó** dinamikus csoport lehetővé teszi, hogy a bérlői rendszergazda egyetlen kattintással a bérlő összes felhasználót tartalmazó csoport létrehozásához. Alapértelmezés szerint a **minden felhasználó** csoport összes olyan felhasználót tartalmazza a könyvtárban, beleértve a tagok és a vendégek.
Az új Azure Active Directory felügyeleti portálon, ha szeretné engedélyezni a **minden felhasználó** csoportba, a beállítások nézetben.

![Megjeleníti a minden felhasználó csoportban Igen engedélyezése](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Az összes felhasználó dinamikus csoport korlátozására
Alapértelmezés szerint a **minden felhasználó** csoport tartalmazza a B2B együttműködés (vendég) felhasználókat is. További biztonságossá teheti a **minden felhasználó** csoport eltávolítása a Vendég szabály segítségével. A következő ábra azt mutatja be a **minden felhasználó** csoport módosítás vendégek kizárása.

![Ha felhasználói típus nem egyenlő Vendég mutat be a szabály](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

Is hasznosak lehetnek az új dinamikus csoport, amely tartalmazza azokat a csak a vendégfelhasználók, hogy (például az Azure AD feltételes hozzáférési házirendek) házirendeket is alkalmazhat létrehozása.
Milyen ilyen csoporthoz nézhet ki:

![Ha a felhasználó típus Vendég mutat be a szabály](media/active-directory-b2b-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>További lépések

- [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
- [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
- [Feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md)

