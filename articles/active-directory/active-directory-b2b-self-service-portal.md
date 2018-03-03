---
title: "Önkiszolgáló regisztrációs portálon az Azure Active Directory B2B együttműködés |} Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat"
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
ms.date: 05/24/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: bb63a3b23bdcaac5c94d43bb8e7294a82b0c3fa0
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Az Azure AD B2B együttműködés előfizetési önkiszolgáló portál

Az ügyfelek sokkal teheti a beépített szolgáltatásai a rendszergazda keresztül közzétett [Azure-portálon](https://portal.azure.com) és a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com) a végfelhasználók számára. De azt is vegye figyelembe, hogy vállalatok testre kell szabnia a bevezetési munkafolyamat B2B felhasználók számára a szervezeti igényeknek. Akkor teheti meg, hogy a [a meghívó API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Az ügyfelek folytatott beszélgetéseket, egy közös szükség van, amely másolatot mindenekelőtt mások nő. Hívja fel a szervezet előfordulhat, hogy nem tudja időben az egyes külső együttműködők akik számára az erőforrásokhoz való hozzáférésre van szükségük. A felhasználók partnervállalatokból regisztrálni magát, amely a hívja fel a szervezet házirendjei úgy végrehajthat. Ebben a forgatókönyvben az API-k segítségével lehetséges, így azt a projektet a Githubon, amely csak elvégző közzétevő: [Github mintaprojektet](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

Ez a Github-projekt bemutatja, hogyan szervezetek az API-k, és adja meg egy csoportházirend-alapú, az önkiszolgáló bejelentkezési képesség a megbízható partnerek, szabályok, amelyek meghatározzák, az alkalmazások eléréséhez. Partner felhasználók is kapnak erőforrásokhoz való hozzáférés, amikor szükségük van rá, biztonságosan, anélkül, hogy a manuális előkészítésére hívja fel szervezet őket. A projekt az Azure-előfizetés tetszés szerinti könnyen telepíthető.

## <a name="as-is-code"></a>Mint-kód

Ne feledje, hogy ez a kód bemutatása az Azure Active Directory B2B meghívó API használatát egy minta elérhetővé tegyen. A fejlesztői csapat vagy egy partner által kell szabható testre, és egy éles telepítési forgatókönyvhöz a telepített előtt meg kell vizsgálni.

## <a name="next-steps"></a>További lépések

Keresse meg az Azure AD B2B együttműködés további cikkeit:
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