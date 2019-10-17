---
title: Az Azure AD App Developers támogatási és Súgó lehetőségei | Microsoft Docs
description: Ismerje meg, hogyan szerezhet segítséget és támogatást a fejlesztéssel kapcsolatos kérdésekre és problémákról a Microsoft-identitásokkal integrálható alkalmazások létrehozásakor (Azure Active Directory és Microsoft-fiók)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429982"
---
# <a name="support-and-help-options-for-developers"></a>Támogatási és súgó lehetőségek fejlesztőknek

Ha most kezdi az integrációt a Azure Active Directory (Azure AD), a Microsoft-identitásokkal vagy a Microsoft Graph API-val, vagy ha új funkciót valósít meg az alkalmazáshoz, akkor a Közösség segítségére van szüksége, vagy megismerheti a a fejlesztőknek nyújtott támogatási lehetőségek. Ez a cikk segít megismerni ezeket a lehetőségeket, beleértve a következőket:

> [!div class="checklist"]
> * Hogyan lehet megkeresni a kérdést, hogy a Közösség nem válaszolt-e, vagy ha már létezik a megvalósítani kívánt szolgáltatás meglévő dokumentációja
> * Bizonyos esetekben csak azt szeretné, hogy támogatási eszközeink segítségével hibakeresést végezzen egy adott probléma esetén
> * Ha nem találja a szükséges választ, felteheti kérdéseit *stack overflow*
> * Ha a hitelesítési kódtárak egyikével kapcsolatos problémát talál, akkor adjon meg egy *GitHub* -problémát
> * Végül, ha valakivel szeretne kommunikálni, érdemes lehet egy támogatási kérést megnyitni

## <a name="search"></a>Search

Ha a fejlesztéssel kapcsolatos kérdése van, előfordulhat, hogy megtalálja a választ a dokumentációban, a GitHub- [mintákat](https://github.com/azure-samples), vagy választ [stack overflow](https://www.stackoverflow.com) kérdéseire.

### <a name="scoped-search"></a>Hatókörön belüli keresés

A gyorsabb eredmény érdekében a keresés hatókörét Stack Overflow, a dokumentációt és a kód mintákat a következő lekérdezéssel használhatja a kedvenc keresőmotorjában:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Ahol *{a keresési kifejezések}* megfelelnek a keresési kulcsszavaknak.

## <a name="use-the-development-support-tools"></a>A fejlesztői támogatási eszközök használata

| Eszköz  | Leírás  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | A jogcímek nevének és értékének dekódolásához illesszen be egy azonosítót vagy hozzáférési tokent. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Eszköz, amely lehetővé teszi a kérelmek elfogadását és a válaszok megtekintését a Microsoft Graph API-ra. |

## <a name="post-a-question-to-stack-overflow"></a>Kérdés közzététele Stack Overflow

Stack Overflow a fejlesztéssel kapcsolatos kérdések előnyben részesített csatornája. Itt a fejlesztői közösség és a Microsoft csapattagok tagjai közvetlenül részt vesznek a problémák megoldásában.

Ha nem talál választ a kérdésére a kereséssel, küldjön új kérdést Stack Overflow. A következő címkék egyikével teheti fel a kérdést, hogy a Közösség könnyebben azonosítsa és válaszolja meg a kérdést:

|Összetevő/régió  | Címkék |
|---------|---------|
| ADAL-könyvtár | [adal](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-könyvtár     | [msal](https://stackoverflow.com/questions/tagged/msal) |
| OWIN middleware  | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[Azure-ad-B2B]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[Azure-ad-B2C]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[Microsoft-Graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| A hitelesítési vagy engedélyezési témakörökhöz kapcsolódó egyéb területek | [[Azure-Active-Directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

A következő bejegyzések Stack Overflow tartalmaznak tippeket, hogyan tehet fel kérdéseket és hogyan adhat hozzá forráskódot. Kövesse az alábbi irányelveket, hogy növelje a közösségi tagok számára a kérdés gyors értékelését és megválaszolását:

* [Hogyan tegyen fel egy jó kérdést](https://stackoverflow.com/help/how-to-ask)
* [Minimális, teljes és ellenőrizhető példa létrehozása](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub-probléma létrehozása

Ha a kódtárakkal kapcsolatos hibát vagy problémát talál, akkor a GitHub-adattárakban probléma merülhet fel. Mivel a kódtárak nyílt forráskódúak, lekéréses kérelmet is küldhet.

A kódtárak és a GitHub-adattárak listáját a következő témakörben találja:

* [ADAL](active-directory-authentication-libraries.md) -kódtárak és GitHub-adattárak
* [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)és [MSAL. obj c](https://github.com/AzureAD/microsoft-authentication-library-for-objc) -könyvtárak és GitHub-adattárak

## <a name="open-a-support-request"></a>Támogatási kérelem megnyitása

Ha valakivel szeretne kommunikálni, nyisson meg egy támogatási kérést. Ha Ön Azure-ügyfél, számos támogatási lehetőség közül választhat. A csomagok összehasonlításához tekintse meg [ezt a lapot](https://azure.microsoft.com/support/plans/). A fejlesztői támogatás az Azure-ügyfelek számára is elérhető. A fejlesztői támogatási csomagok megvásárlásával kapcsolatos információkért tekintse meg [ezt a lapot](https://azure.microsoft.com/support/plans/developer/).

* Ha már rendelkezik Azure-támogatási csomaggal, [Nyisson meg egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Ha Ön nem Azure-ügyfél, akkor a Microsoft [kereskedelmi támogatási szolgálatán](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)keresztül is megnyithatja a támogatási kérést.

Egy [virtuális ügynököt](https://support.microsoft.com/contactus/?ws=support) is kipróbálhat, hogy támogatást kapjon vagy kérdéseket tegyen fel.
