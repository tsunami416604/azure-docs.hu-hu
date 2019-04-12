---
title: Támogatás és súgó lehetőségeket az Azure AD-alkalmazások fejlesztőinek |} A Microsoft Docs
description: Tudja, hogyan szerezze be a Súgó és támogatás a fejlesztéssel kapcsolatos kérdések és problémák, amelyek integrálhatók a Microsoft-identitás (Azure Active Directory és a Microsoft-fiók) alkalmazás létrehozásakor
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2019
ms.author: celested
ms.reviewer: jmprieur, dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0de9b3e3535253724d068f806b219736f6889477
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501589"
---
# <a name="support-and-help-options-for-developers"></a>Fejlesztői támogatás és súgó lehetőségei

Ha még csak most kezdi integrálása az Azure Active Directory (Azure AD), a Microsoft identitások vagy a Microsoft Graph API, vagy ha az alkalmazás egy új szolgáltatás implementálja, esetekben szükség súgó beszerzése a Közösségtől, vagy megismerheti a támogatási lehetőségek, hogy a fejlesztők rendelkezésére. Ez a cikk segít megismerni az ezeket a beállításokat, beleértve:

> [!div class="checklist"]
> * Létezik-e a kérdést a Közösségnek nincs megválaszolva, vagy ha egy meglévő dokumentáció a szolgáltatás kívánt már megvalósítása keresésének módjai
> * Bizonyos esetekben csak át szeretné a támogatási eszközök segítségével is egy konkrét problémával kapcsolatban
> * Ha nem találja a választ, amely van szüksége, érdemes a kérdés feltevése *Stack overflow-n*
> * Ha úgy találja, a hitelesítési tárak egyik hibát, növelje a *GitHub* probléma
> * Végül szüksége, vegye fel a kapcsolatot, előfordulhat, hogy szeretné-e, nyisson egy támogatási kérést

## <a name="search"></a>Keresés

Ha fejlesztéssel kapcsolatos kérdése van, akkor előfordulhat, hogy tudni találja a választ a dokumentációban [GitHub-minták](https://github.com/azure-samples), vagy adott válaszok [Stack Overflow](https://www.stackoverflow.com) kérdéseket.

### <a name="scoped-search"></a>Hatókörön belüli keresés

A gyorsabb eredmény érdekében hatókörét a keresést, hogy a Stack overflow-n, a dokumentáció és Kódminták a kedvenc keresőmotor használatával a következő lekérdezést:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Ahol *{Your keresőkifejezéseket}* felel meg a keresési kulcsszavakat.

## <a name="use-the-development-support-tools"></a>A fejlesztői támogatási eszközök használata

| Eszköz  | Leírás  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Illessze be az Azonosítót, vagy a hozzáférési tokent való dekódolandó jogcímek neveket és értékeket. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Eszköz, amely lehetővé teszi a kérelmeket, és tekintse meg a válaszok a Microsoft Graph API ellen. |

## <a name="post-a-question-to-stack-overflow"></a>Új kérdést tenne fel a Stack overflow-n

A stack Overflow az előnyben részesített csatorna fejlesztéssel kapcsolatos kérdéseket. Itt a fejlesztői közösség tagjai, és a Microsoft a csapattagok közvetlenül érintett annak megakadályozása, hogy a problémák megoldásában.

Ha nem találja a választ a kérdésére keresési keresztül, küldje el a Stack Overflow egy másik kérdést. Használja a következő címkék egyikét, ha kérdéseket feltenni a közösségi azonosításához, és gyorsabban választ a kérdésére segítségével:

|Összetevő/terület  | Címkék |
|---------|---------|
| ADAL-könyvtár | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Az MSAL könyvtár     | [[az msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN közbenső szoftvert  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[a microsoft graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| A hitelesítési vagy engedélyezési kapcsolatos más területeken kapcsolatos témakörök | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

A Stack overflow-n a következő bejegyzések kérdéseket tehet fel és hozzáadása a forráskódja tippeket tartalmaz. Kövesse ezeket az irányelveket növelheti annak esélyét segítségével mérheti fel, és gyorsan válaszolni a Közösség tagjai:

* [Hogyan jó kérdések feltevése](https://stackoverflow.com/help/how-to-ask)
* [A minimális, teljes és ellenőrizhető példa létrehozása](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Egy GitHub-probléma létrehozása

Ha egy hiba vagy probléma a könyvtárakhoz kapcsolódó talál, növelje a problémát a GitHub-tárházakban. Mivel a könyvtárak nyílt forráskódú, elküldheti a lekéréses kérelem is.

A szalagtárak és a GitHub-adattárak listájának megtekintéséhez, a következő cikkekben talál:

* [ADAL](active-directory-authentication-libraries.md) , valamint a GitHub-adattárak
* [Az MSAL](reference-v2-libraries.md) , valamint a GitHub-adattárak

## <a name="open-a-support-request"></a>Hozzon létre egy támogatási kérést

Ha szüksége, vegye fel a kapcsolatot, megnyithatja egy támogatási kérést. Ha Ön Azure-ügyfeleket, többféle módon támogatás érhető el. Csomagok összehasonlítása, lásd: [ezt oldal](https://azure.microsoft.com/support/plans/). Fejlesztői támogatás is az Azure-ügyfelek számára érhető el. Fejlesztői támogatási csomagok kapcsolatos információkért lásd: [ezt oldal](https://azure.microsoft.com/support/plans/developer/).

* Ha már rendelkezik egy Azure támogatási csomag, [nyisson egy támogatáskérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Ha nem Azure-ügyfeleket, is megnyithat egy támogatási kérést a Microsoft csapatához [a kereskedelmi támogatási](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Megpróbálhat egy [virtuális ügynök](https://support.microsoft.com/contactus/?ws=support) juthat támogatáshoz, vagy tegyen fel kérdéseket.

### <a name="free-chat-support-for-a-limited-time"></a>Korlátozott ideig ingyenes csevegéses támogatás

A csevegéses támogatás, amely korlátozott ideig ingyenesen használható Microsoft Partners is használhatja. Ha a vállalata nem Microsoft-Partner, regisztrálása, ingyenes, és szerezze be az egyéb előnyök címen [Itt](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Miután regisztrált a vállalat, indítsa el a Csevegés kérelem [Itt](https://aka.ms/devchat).
