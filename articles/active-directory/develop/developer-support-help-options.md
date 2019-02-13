---
title: Támogatás és súgó lehetőségeket az Azure-identitás fejlesztők számára |} A Microsoft Docs
description: Tudja, hogyan szerezze be a Súgó és támogatás a fejlesztéssel kapcsolatos kérdések és problémák, amelyek integrálhatók a Microsoft Azure-identitás (Azure Active Directory és MSA) alkalmazás létrehozásakor
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
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f777c851caa8613156128b266b7841d9b665754e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168714"
---
# <a name="support-and-help-options-for-developers"></a>Fejlesztői támogatás és súgó lehetőségei

Függetlenül attól, hogy ha csupán rendszertől integrálása az Azure Active Directory, Microsoft identitások vagy a Microsoft Graph API-t, vagy amikor azt fontolgatja, hogy egy új szolgáltatást szeretne az alkalmazáshoz, vannak-e, hogy szüksége súgó beszerzése a Közösségtől, és megismerheti a támogatási lehetőségek, hogy a fejlesztők rendelkezésére. Ez a cikk segít megismerni, ezek a beállítások összegzése alatt:

> [!div class="checklist"]
> * Keresés ellenőrizheti, ha a probléma kérdés nem érkezett válasz a Közösség által, vagy ha egy meglévő dokumentáció a szolgáltatás kívánt megvalósítása már létezik
> * Bizonyos esetekben csak át szeretné használni az eszközök segítséget nyújtanak egy adott hibakeresése a problémát
> * Ha nem találja a választ a következőkre lesz szüksége, érdemes a kérdés feltevése *Stack overflow-n*
> * Ha úgy találja, a hitelesítési tárak egyik hibát, növelje a *GitHub* probléma
> * Végül szüksége, vegye fel a kapcsolatot, előfordulhat, hogy szeretné-e, nyisson egy támogatási kérést


## <a name="search"></a>Keresés

Ha fejlesztéssel kapcsolatos kérdése van, akkor lehet tudja megtalálni a választ a dokumentációban, szüksége a [GitHub-minták](https://github.com/azure-samples), vagy adott válaszok [Stack Overflow](https://www.stackoverflow.com) kérdéseket.

### <a name="scoped-search"></a>Hatókörön belüli keresés
Gyorsabb eredmények hatókörét a keresést, hogy a Stack overflow-n, a dokumentációban és a a Kódminták a következő használatával a [kedvenc keresőmotor](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Ahol *{Your keresőkifejezéseket}* van a keresési kulcsszavakat.
<br/>

## <a name="use-our-development-support-tools"></a>A fejlesztői támogatási eszközök használata

|Eszköz  |Leírás  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Illessze be a való dekódolandó jogcímek neveket és értékeket egy azonosító vagy a hozzáférési jogkivonatok |
|[Hiba történt a kód elemző](https://apps.dev.microsoft.com/portal/tools/errors)| Illessze be a bejelentkezés során kapott hibakódot vagy hozzájárulás megadása a lapok megjelenítéséhez a lehetséges okok és szervizelések |
|[A Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Eszköz, amely lehetővé teszi a kérelmeket, és tekintse meg a válaszok a Microsoft Graph API ellen|

<br/>

[![Stack overflow-n](./media/developer-support-help-options/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Új kérdést tenne fel a Stack overflow-n

A stack Overflow az előnyben részesített csatorna fejlesztéssel kapcsolatos kérdések – ahol mindkét, Microsoft-Közösség tagjai egy csapattag közvetlenül érintett az segít megoldani a problémát.

Ha nem talál választ a problémára felvétellel, küldje el a Stack Overflow egy másik kérdést: használja az alábbi kérdésekre a Közösség azonosítását, amikor címkék majd ad választ a kérdésére az időben:

|Összetevő/terület  |Címkék  |
|---------|---------|
|ADAL-könyvtár |[[adal]](https://stackoverflow.com/questions/tagged/adal)|
|Az MSAL könyvtár     |[[msal]](https://stackoverflow.com/questions/tagged/msal)|
|OWIN közbenső szoftvert  |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph)
|A hitelesítési vagy engedélyezési kapcsolatos más területeken kapcsolatos témakörök |[[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> A Stack overflow-n a következő bejegyzések tippekkel szolgál az, hogy kérdéseket tartalmaz, és a hozzáadása a forráskódja - ezeket az irányelveket a következő tippek segíthetnek, növelheti annak esélyét segítségével mérheti fel, és gyorsan válaszolni a Közösség tagjai:
> - [Hogyan jó kérdések feltevése](https://stackoverflow.com/help/how-to-ask)
> - [A minimális, a teljes és a ellenőrizhető példa létrehozása](https://stackoverflow.com/help/mcve)

<br/>


[![Stack overflow-n](./media/developer-support-help-options/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Egy GitHub-probléma létrehozása

 Ha egy hiba vagy probléma a könyvtárakhoz kapcsolódó, emelje egy problémát a GitHub-tárházakban. Mivel a könyvtárak nyílt forráskódú, szabadon is, valamint a lekéréses kérelmet küldeni. A következő cikkben, valamint a saját GitHub-adattárak listáját tartalmazza:

- [Adal-t, az MSAL és Owin közbenső](active-directory-authentication-libraries.md) , valamint a GitHub-adattárak

<br/>

## <a name="open-a-support-request"></a>Hozzon létre egy támogatási kérést

Ha szüksége, vegye fel a kapcsolatot, megnyithatja egy támogatási kérést. Ha Ön Azure-ügyfeleket, többféle módon támogatás érhető el. Csomagok összehasonlítása, lásd: [ezt oldal](https://azure.microsoft.com/support/plans/). Fejlesztői támogatás is az Azure-ügyfelek számára érhető el. Fejlesztői támogatási csomagok kapcsolatos információkért lásd: [ezt oldal](https://azure.microsoft.com/support/plans/developer/).

- Ha már rendelkezik egy Azure támogatási csomag, [nyisson egy támogatáskérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Ha nem Azure-ügyfeleket, is megnyithat egy támogatási kérést a Microsoft csapatához [a kereskedelmi támogatási](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Megpróbálhatja [a virtuális ügynök](https://support.microsoft.com/contactus/?ws=support) juthat támogatáshoz, vagy tegyen fel kérdéseket.

### <a name="free-chat-support-for-a-limited-time"></a>Korlátozott ideig ingyenes csevegéses támogatás

A csevegéses támogatás – amely korlátozott ideig ingyenesen használható Microsoft Partners is használhatja. Ha a vállalata nem Microsoft-Partner, regisztrálása, ingyenes, és szerezze be az egyéb előnyök címen [Itt](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Miután regisztrált a vállalat, indítsa el a Csevegés kérelem [Itt](https://aka.ms/devchat).
