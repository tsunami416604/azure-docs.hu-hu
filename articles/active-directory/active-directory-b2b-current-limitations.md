---
title: "Azure Active Directory B2B együttműködés korlátozásai |} Microsoft Docs"
description: "Azure Active Directory B2B együttműködés aktuális korlátozásai"
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
ms.openlocfilehash: 581e5d1fb5fb08d0dc89ed2c85edcb5f0005650b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B együttműködés korlátozásai
Az Azure Active Directory (Azure AD) B2B együttműködés jelenleg a jelen cikkben ismertetett korlátozások vonatkoznak.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges dupla többtényezős hitelesítés
Az Azure AD B2B kényszerítheti a többtényezős hitelesítést, az erőforrás-szervezet (a hívja fel szervezet). Ez a megközelítés okait részletezi [feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md). Ha egy partnert már többtényezős hitelesítés beállítása, és azt, a felhasználók esetleg el kell végeznie a hitelesítési egyszer a otthoni szervezetek, majd ismét az Öné.

## <a name="instant-on"></a>Azonnali
A B2B együttműködés adatfolyamok azt felhasználók hozzáadása a címtárhoz és dinamikusan frissítse azokat a meghívó érvényesítési és alkalmazás-hozzárendelés, stb. A frissítések és az írás szokásos fordul elő egy címtárpéldány, és minden példányára kell replikálni. Replikáció befejezése után minden példány frissítése. Egyes esetekben, amikor a objektumot írt vagy egy példány frissítése, és ez az objektum beolvasása a hívást, hogy egy másik példánya, a replikációs késések fordulnak elő akkor fordulhat elő. Ha ez előfordul, frissítse vagy segítségével próbálja meg újra. Ha az alkalmazást az API felületen, néhány biztonsági off újrapróbálkozások egy megfelelő, a védelem gyakorlat, hogy enyhítse a probléma.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2bB együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
