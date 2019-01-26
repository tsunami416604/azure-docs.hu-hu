---
title: Dinamikus csoportok és az Azure Active Directory B2B együttműködés |} A Microsoft Docs
description: Bemutatja, hogyan használhatja az Azure AD dinamikus csoportok az Azure Active Directory B2B együttműködés
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 206737049a446476b4b188a72effdc231cb5dc3b
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082010"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dinamikus csoportok és az Azure Active Directory B2B-együttműködés

## <a name="what-are-dynamic-groups"></a>Mik azok a dinamikus csoportok?
Biztonságicsoport-tagság dinamikus konfigurálása az Azure Active Directory (Azure AD) érhető el a [az Azure Portalon](https://portal.azure.com). Rendszergazdák az Azure AD felhasználói attribútumok (például a userType, részleg vagy ország) alapján létrehozott csoportok feltöltéséhez szabályokat állíthatnak be. Tagok automatikusan lehet hozzáadni vagy attribútumaik alapján biztonsági csoportból eltávolított. Ezeket a csoportokat is hozzáférést biztosít alkalmazások vagy a felhőbeli erőforrások (SharePoint-helyek, dokumentumok) és a licenceket hozzárendelheti a tagokat. További információ a dinamikus csoportok [az Azure Active Directory dedikált](../active-directory-accessmanagement-dedicated-groups.md).

A megfelelő [licencelése az Azure AD Premium P1 vagy P2](https://azure.microsoft.com/pricing/details/active-directory/) dinamikus csoportok létrehozása és használata szükséges. További információ: a cikk [dinamikus csoport tagságához Attribútumalapú szabályok létrehozása az Azure Active Directoryban](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Mik azok a beépített dinamikus csoportokat?
A **minden felhasználó** dinamikus csoport lehetővé teszi, hogy a bérlői rendszergazdák hozhat létre egy egyetlen kattintással a bérlő összes felhasználóját tartalmazó csoporthoz. Alapértelmezés szerint a **minden felhasználó** csoport a címtárban, beleértve a tagok és a vendégek felhasználókat tartalmazza.
Az új Azure Active Directory felügyeleti portálon, ha szeretné, engedélyezze a **minden felhasználó** csoporthoz a beállítások nézetben.

![Látható Igen minden felhasználó csoport engedélyezése](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>A felhasználók az összes dinamikus csoport megerősítése
Alapértelmezés szerint a **minden felhasználó** csoport tartalmazza a B2B együttműködés (vendég) felhasználókat is. További gondoskodhat a **minden felhasználó** csoport eltávolítása a Vendég szabály segítségével. A következő ábra azt mutatja be a **minden felhasználó** csoport módosítás vendégek kizárása.

![Ahol felhasználótípus nem egyenlő Vendég mutat be a szabály](media/use-dynamic-groups/exclude-guest-users.png)

Akkor is hasznosak lehetnek, amely tartalmazza azokat a csak a vendégfelhasználók, hogy a szabályzatok (például az Azure AD feltételes hozzáférési szabályzatok) alkalmazása új dinamikus csoportot hozhat létre.
Egy ilyen csoport néz:

![Ahol a felhasználó típusa egyenlő Vendég mutat be a szabály](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)
- [B2B-együttműködés felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)
- [Feltételes hozzáférés B2B-együttműködés felhasználók](conditional-access.md)

