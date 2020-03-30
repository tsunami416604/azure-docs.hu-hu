---
title: Támogatási és súgólehetőségek az Azure AD alkalmazásfejlesztők számára | Microsoft dokumentumok
description: Ismerje meg, hogyan kaphat segítséget és támogatást a fejlesztéssel kapcsolatos kérdésekhez és problémákhoz a Microsoft identitásokkal (Azure Active Directoryval és Microsoft-fiókkal) integráló alkalmazások létrehozásakor
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 89bf49fb44d8575b251a0b33698bc4ce8425cc2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160967"
---
# <a name="support-and-help-options-for-developers"></a>Támogatási és súgó lehetőségek fejlesztőknek

Ha csak most kezdi el integrálni az Azure Active Directoryt (Azure AD), a Microsoft identitásokat vagy a Microsoft Graph API-t, vagy amikor új funkciót valósít meg az alkalmazásában, vannak esetek, amikor segítséget kell kérnie a közösségtől, vagy támogatási lehetőségeket, amelyeket fejlesztőként rendelkezik. Ez a cikk segít megérteni ezeket a lehetőségeket, többek között:

> [!div class="checklist"]
> * Hogyan kereshető meg, hogy a közösség nem válaszolt-e a kérdésére, vagy hogy létezik-e már meglévő dokumentáció a végrehajtani kívánt funkcióhoz.
> * Bizonyos esetekben csak támogatási eszközeinkkel szeretné használni egy adott probléma hibakeresését
> * Ha nem találja a szükséges választ, feltehet egy kérdést a *Stack Overflow-ról*
> * Ha problémát talál az egyik hitelesítési kódtárakban, emeljefel a *GitHub-problémát*
> * Végül, ha beszélni szeretne valakivel, érdemes megnyitnia egy támogatási kérelmet

## <a name="search"></a>Keresés

Ha fejlesztési kérdéssel kapcsolatos kérdése van, előfordulhat, hogy megtalálja a választ a dokumentációban, a [GitHub-mintákban](https://github.com/azure-samples), vagy a [Stack Overflow](https://www.stackoverflow.com) kérdésekre adott válaszokban.

### <a name="scoped-search"></a>Hatóköralapú keresés

A gyorsabb eredmények érdekében a keresés hatóköre a Veremtúlcsordulás, a dokumentáció és a kódmintákra a következő lekérdezés használatával a kedvenc keresőmotorjában:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Ahol *{A keresési feltételek}* megfelelnek a keresési kulcsszavakat.

## <a name="use-the-development-support-tools"></a>A fejlesztési támogatási eszközök használata

| Eszköz  | Leírás  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Illessze be az azonosítót vagy a hozzáférési jogkivonatot a jogcímek nevének és értékeinek dekódolásához. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Olyan eszköz, amely lehetővé teszi a kérések és a válaszok megtekintését a Microsoft Graph API-val szemben. |

## <a name="post-a-question-to-stack-overflow"></a>Kérdés feladása a Veremtúlcsorduláshoz

Stack túlcsordulás az előnyben részesített csatorna a fejlesztéssel kapcsolatos kérdésekre. Itt a fejlesztői közösség és a Microsoft csapatának tagjai közvetlenül részt vesznek a problémák megoldásában.

Ha nem talál választ a kérdésére a kereséssorán, küldjön be egy új kérdést a Veremtúlcsordulás mezőbe. Az alábbi címkék egyikével tehet fel kérdéseket, hogy a közösség gyorsabban azonosíthassa és megválaszolhassa a kérdést:

|Összetevő/terület  | Címkék |
|---------|---------|
| ADAL-könyvtár | [- Nem, nem, nem, nem, nem, nem, nem](https://stackoverflow.com/questions/tagged/adal) |
| MSAL könyvtár     | [Nem, nem, nem, nem, nem, nem, nem,](https://stackoverflow.com/questions/tagged/msal) |
| OWIN köztes szoftver  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azúr-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azúr-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| A hitelesítéssel vagy engedélyezéssel kapcsolatos bármely más terület | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

A Stack Overflow következő bejegyzései tippeket tartalmaznak arra vonatkozóan, hogyan tehet fel kérdéseket, és hogyan adhat hozzá forráskódot. Kövesse az alábbi irányelveket, hogy növelje annak esélyét, hogy a közösség tagjai gyorsan értékeljék és válaszoljanak a kérdésére:

* [Hogyan tudok feltenni egy jó kérdést](https://stackoverflow.com/help/how-to-ask)
* [Hogyan hozzunk létre egy minimális, teljes és ellenőrizhető példa](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>GitHub-probléma létrehozása

Ha hibát vagy problémát talál a könyvtárainkkal kapcsolatban, felvet egy problémát a GitHub-adattárakban. Mivel könyvtáraink nyílt forráskódúak, lekéréses kérelmet is benyújthat.

A tárak és a GitHub-adattárak listáját az alábbi témakörben tetszetős:

* [Azure Active Directory hitelesítési könyvtár (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) kódtárai és GitHub-adattárak
* [Microsoft authentication library (MSAL)](reference-v2-libraries.md) tárak és GitHub-adattárak

## <a name="open-a-support-request"></a>Támogatási kérelem megnyitása

Ha beszélni szeretne valakivel, megnyithat egy támogatási kérelmet. Ha Ön Azure-ügyfél, számos támogatási lehetőség áll rendelkezésre. A tervek összehasonlításához tekintse meg [ezt az oldalt.](https://azure.microsoft.com/support/plans/) Fejlesztői támogatás is elérhető az Azure-ügyfelek számára. A fejlesztői támogatási csomagok megvásárlásáról az ezen az oldalon olvashat [bővebben.](https://azure.microsoft.com/support/plans/developer/)

* Ha már rendelkezik Azure-támogatási csomaggal, [itt nyisson meg egy támogatási kérelmet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Ha Ön nem Azure-ügyfél, a Microsoftnál is megnyithat egy támogatási kérelmet [a kereskedelmi támogatásunkon](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)keresztül.

Megpróbálhat egy [virtuális ügynököt](https://support.microsoft.com/contactus/?ws=support) is, hogy támogatást szerezzen, vagy kérdéseket tegyen fel.
