---
title: Önkiszolgáló regisztrációs portálon az Azure Active Directory B2B együttműködés |} Microsoft Docs
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/08/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 5ee55034e84fe09484a2f7613cc2224be70fdebb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260072"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Az Azure AD B2B együttműködés előfizetési önkiszolgáló portál

Az ügyfelek a beépített szolgáltatásokkal keresztül közzétett sokkal teheti a [Azure-portálon](https://portal.azure.com) és a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com) a végfelhasználók számára. Azonban szükség lehet a bevezetési munkafolyamat B2B számára, hogy a szervezet igényeihez testreszabása. Ehhez a [a meghívó API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Hívja fel a szervezeten, előfordulhat, hogy nem tudja időben az egyes külső együttműködők számára az erőforrások elérését igénylő. A felhasználók partnervállalatokból regisztrálni magát, amely a Ön mint hívja fel a szervezet házirendjei szükségük van egy módszerre. Ebben a forgatókönyvben lehetőség az API-k segítségével. Van egy [mintaprojektet a Githubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) does, amely éppen ez.

Ez a GitHub-projekt jeleníti meg, hogyan szervezetek használatával az API-k adjon meg egy csoportházirend-alapú, az önkiszolgáló bejelentkezési képesség a megbízható partnerek számára, a szabályokat, amelyekkel meghatározhatja, hogy az alkalmazások eléréséhez. Partner felhasználók is kapnak erőforrások elérését, amikor szükségük van rá. Ehhez biztonságos, anélkül, hogy a manuális előkészítésére hívja fel szervezet őket. A projekt az Azure-előfizetés tetszés szerinti könnyen telepíthető.

## <a name="as-is-code"></a>Mint-kód

Ez a kód bemutatása az Azure Active Directory B2B meghívó API használatát egy minta elérhetővé tegyen. A fejlesztői csapat vagy egy partner által kell szabható testre, és csak egy éles telepítési forgatókönyvhöz telepítheti azt javasoljuk.

## <a name="next-steps"></a>További lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure AD B2B együttműködés licencelés](licensing-guidance.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](faq.md)