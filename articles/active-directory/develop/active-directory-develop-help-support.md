---
title: "Támogatás és Súgó beállítások Azure identitás-fejlesztőknek |} Microsoft Docs"
description: "Ismeri az beszerzése a Súgó és támogatás fejlesztéssel kapcsolatos kérdések és problémák az integrációt a Microsoft Azure-identitás (Azure Active Directory és a felügyelt Szolgáltatásfiók) alkalmazás létrehozásakor"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7c382da9bd9032b30f7c620e839c41c1756ba3f6
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="support-and-help-options-for-developers"></a>A fejlesztők támogatás és Súgó beállítások 

Függetlenül attól, ha csak indításakor integrálása az Azure Active Directory, Microsoft identitások vagy a Microsoft Graph API-t vagy az alkalmazás valósít meg új funkciója, amikor nincsenek-e Súgó kérése a közösségi vagy megértése szükséges idő a támogatási lehetőségek fejlesztőként rendelkező. Ez a cikk ezekről a lehetőségekről, alábbiakban összefoglalást nyújt segítséget:

> [!div class="checklist"]
> * Keresési kereséséhez, ha a probléma kérdés nem érkezett válasz a Közösség által, vagy ha a szolgáltatás egy meglévő dokumentációjában talál próbál megvalósítani már létezik
> * Néhány esetben szeretné az eszközök használatával alakítsa ki egy adott a probléma hibakeresését
> * Ha nem találja a választ az alábbiakra lesz szüksége, érdemes lehet a kérdés *Stack Overflow*
> * Ha valamelyik, a hitelesítési könyvtárat problémát talál, növelje a *GitHub* probléma
> * Végül ha szüksége, vegye fel a kapcsolatot, érdemes egy támogatási kérést nyithat


## <a name="search"></a>Keresés

Fejlesztéssel kapcsolatos kérdése van, ha nem található a válasz van szüksége a dokumentációról, esetleg az [github minták](https://github.com/azure-samples), vagy adott válaszok [Stack Overflow](https://www.stackoverflow.com) kérdéseket.

### <a name="scoped-search"></a>Célzott keresést
A gyorsabb eredmény érdekében hatókör a Stack Overflow dokumentációban és az mintakódok keresést a következő használatával a [kedvenc keresőmotorja](https://bing.com):
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
Ha *{a keresési feltételek}* van a keresési kulcsszavakat.
<br/>

## <a name="use-our-development-support-tools"></a>A fejlesztői támogatási eszközök használata

|Eszköz  |Leírás  |
|---------|---------|
|[jwt.MS](https://jwt.ms)| Illessze be egy Azonosítót vagy a hozzáférési jogkivonatok dekódolni a jogcímeket és értékek |
|[Hiba kód elemző eszköz](https://apps.dev.microsoft.com/portal/tools/errors)| Illessze be a bejelentkezés során kapott hibakódot vagy hozzájárulás lapok megjelenítéséhez a lehetséges okok és szervizelések |
|[A Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Eszköz, amely lehetővé teszi a kéréseket, és tekintse meg a Microsoft Graph API elleni válaszok|

<br/>

[![Stack overflow webhelyre](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>A kérdés küldje el a Stack Overflow

Ha mindkét Microsoft Közösség tagjai csapattagok számára veremtúlcsordulás fejlesztéssel kapcsolatos kérdéseket - előnyben részesített csatorna segít megoldani a problémát a közvetlenül érint.

Ha nem találja a választ egy problémára keresési keresztül, küldje el a Stack Overflow egy másik kérdést: használja a következő kérdések megválaszolásával azonosítására, a közösségi meghozásakor címkéket majd válaszolja meg időben kérdés:

|Összetevő-/ terület  |Címkék  |
|---------|---------|
|ADAL-könyvtár |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|MSAL könyvtár     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN köztes  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Az Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-Active-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Az Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[az azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[A Microsoft Graph API](https://developer.microsoft.com/graph/) |[[a microsoft-diagram]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|A hitelesítési vagy engedélyezési kapcsolatos bármely más terület kapcsolatos témakörök |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> A következő bejegyzéseket a Stack Overflow tippek az kérdések ahhoz, hogyan tartalmazhat, és tippek forráskód - betartásuk hozzáadása az így növelheti az esélye Közösség tagjai segítségével mérheti fel, és gyorsan képesek reagálni a kérdésére:  
> - [Hogyan jó kérdések feltevése](https://stackoverflow.com/help/how-to-ask)
> - [A minimális, teljes és ellenőrizhető példa létrehozása](https://stackoverflow.com/help/mcve)

<br/>


[![Stack overflow webhelyre](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Hozzon létre egy GitHub-probléma

 Ha egy hiba vagy a könyvtárak kapcsolatos problémát talál, emelje meg a GitHub-adattárak problémát. Mivel a szalagtárak nyílt forráskódú, szabadon is beküldhető egy tartalomjavítási kérelem is. A következő cikk tartalmazza a szalagtárak és a GitHub-adattárak listáját:

- [Adal-t, MSAL és Owin köztes](active-directory-authentication-libraries.md) szalagtárak és a GitHub-adattárak

<br/>

## <a name="open-a-support-request"></a>Nyissa meg a támogatási kérelem

Ha szüksége, vegye fel a kapcsolatot, a támogatási kérelem nyithatja meg. Ha az Azure felhasználóinál, többféle módon támogatás érhető el. Az összehasonlítandó terveket, lásd: [ezen a lapon](https://azure.microsoft.com/support/plans/). Fejlesztői támogatás érhető el az Azure-ügyfél. A fejlesztői támogatás ügyfeleknek megvásárlásával információkért lásd: [ezen a lapon](https://azure.microsoft.com/support/plans/developer/).

- Ha már rendelkezik egy Azure támogatja megtervezéséhez [itt támogatási kérést nyithat](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- Ha nem az Azure felhasználóinál, is megnyithatja egy támogatási kérést a Microsoft keresztül [a kereskedelmi támogatási](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Is megpróbálhatja [a virtuális ügynök](https://support.microsoft.com/contactus/?ws=support) kérhet segítséget, vagy kérdései vannak.

### <a name="free-chat-support-for-a-limited-time"></a>Korlátozott ideig szabad Csevegés támogatása

A Csevegés támogatása – amely a Microsoft Partners szabad korlátozott ideig is használható. Ha a vállalata nem egy Microsoft partnert, regisztrálják az ingyenes, és szerezze be az egyéb előnyök címen [Itt](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Miután regisztrált a vállalat, indítsa el a Csevegés kérelem [Itt](https://aka.ms/devchat).