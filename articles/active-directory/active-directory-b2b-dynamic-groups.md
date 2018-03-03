---
title: "Dinamikus csoportok és az Azure Active Directory B2B együttműködés |} Microsoft Docs"
description: "Bemutatja, hogyan használható az Azure AD dinamikus csoportok Azure Active Directory B2B együttműködés"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 12/14/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: cbb700e513947034a783c6b6574c8daddf96844b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
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

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2B együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
