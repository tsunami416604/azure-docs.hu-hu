---
title: "Önkiszolgáló regisztrációs portálon az Azure Active Directory B2B együttműködés |} Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat"
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
ms.date: 05/24/2017
ms.author: sasubram
ms.openlocfilehash: 307373c75bbb87cec683f7a3097f8f159c9d5e61
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Az Azure AD B2B együttműködés előfizetési önkiszolgáló portál

Az ügyfelek sokkal teheti a beépített szolgáltatásai az informatikai rendszergazda keresztül közzétett [Azure-portálon](https://portal.azure.com) és a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com) a végfelhasználók számára. De azt is vegye figyelembe, hogy vállalatok testre kell szabnia a bevezetési munkafolyamat B2B felhasználók számára a szervezeti igényeknek. Akkor teheti meg, hogy a [az API felületen](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Az ügyfelekkel folytatott beszélgetéseket látható egy közös kell növekedésnek mindenekelőtt mások fel. Hívja fel a szervezet előfordulhat, hogy nem tudja időben az egyes külső együttműködők akik számára az erőforrásokhoz való hozzáférésre van szükségük. A felhasználók partnervállalatokból regisztrálni magát, amely a hívja fel a szervezet házirendjei úgy végrehajthat. Ebben a forgatókönyvben az API-k segítségével lehetséges, így azt a projektet a Githubon, amely csak elvégző közzétevő: [Github mintaprojektet](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

A Github-projekt bemutatja, hogyan szervezetek az API-k, és adja meg egy csoportházirend-alapú, az önkiszolgáló bejelentkezési képesség a megbízható partnerek, szabályok, amelyek meghatározzák, az alkalmazások eléréséhez. Partner felhasználók is kapnak erőforrásokhoz való hozzáférés, amikor szükségük van rá, biztonságosan, anélkül, hogy a manuális előkészítésére hívja fel szervezet őket. A projekt az Azure-előfizetés tetszés szerinti könnyen telepíthető.

## <a name="as-is-code"></a>Mint-kód

Ne feledje, hogy ez a kód bemutatása az Azure Active Directory B2B meghívó API használatát egy minta elérhetővé tegyen. A fejlesztői csapat vagy egy partner által kell szabható testre, és egy éles telepítési forgatókönyvhöz a telepített előtt meg kell vizsgálni.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:
* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
* [Hibaelhárítás az Azure Active Directory B2B együttműködés](active-directory-b2b-troubleshooting.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](active-directory-b2b-faq.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)