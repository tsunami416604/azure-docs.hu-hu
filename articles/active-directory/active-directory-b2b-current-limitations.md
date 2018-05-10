---
title: Azure Active Directory B2B együttműködés korlátozásai |} Microsoft Docs
description: Azure Active Directory B2B együttműködés aktuális korlátozásai
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: d4efa6ad1d2768a41a670fae4c490942b5a814ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure AD B2B együttműködés korlátozásai
Az Azure Active Directory (Azure AD) B2B együttműködés jelenleg a jelen cikkben ismertetett korlátozások vonatkoznak.

## <a name="possible-double-multi-factor-authentication"></a>Lehetséges dupla többtényezős hitelesítés
Az Azure AD B2B kényszerítheti a többtényezős hitelesítést, az erőforrás-szervezet (a hívja fel szervezet). Ez a megközelítés okait részletezi [feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md). Ha egy partnert már többtényezős hitelesítés beállítása, és azt, a felhasználók esetleg el kell végeznie a hitelesítési egyszer a otthoni szervezetek, majd ismét az Öné.

## <a name="instant-on"></a>Azonnali
A B2B együttműködés adatfolyamok azt felhasználók hozzáadása a címtárhoz és dinamikusan frissítse azokat a meghívó érvényesítési és alkalmazás-hozzárendelés, stb. A frissítések és az írás szokásos fordul elő egy címtárpéldány, és minden példányára kell replikálni. Replikáció befejezése után minden példány frissítése. Egyes esetekben, amikor a objektumot írt vagy egy példány frissítése, és ez az objektum beolvasása a hívást, hogy egy másik példánya, a replikációs késések fordulnak elő akkor fordulhat elő. Ha ez előfordul, frissítse vagy segítségével próbálja meg újra. Ha az alkalmazást az API felületen, néhány biztonsági off újrapróbálkozások egy megfelelő, a védelem gyakorlat, hogy enyhítse a probléma.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés, tekintse meg a következő cikkeket:

- [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [B2bB együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)

