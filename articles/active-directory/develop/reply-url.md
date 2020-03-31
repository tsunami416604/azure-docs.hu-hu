---
title: Átirányítás URI & válasz URL-korlátozások - Microsoft identity platform | Azure
description: Válasz URL-ek/átirányítási URI-k korlátozásai & korlátozások
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656738"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Átirányítási URI/válasz URL-cím korlátozásai

Az átirányítási URI vagy válasz URL-címe az a hely, ahová az engedélyezési kiszolgáló elküldi a felhasználót, miután az alkalmazás sikeresen engedélyezte, és megkapta az engedélyezési kódot vagy a hozzáférési jogkivonatot. A kódot vagy jogkivonatot tartalmazza az átirányítási URI vagy válasz jogkivonat, ezért fontos, hogy regisztrálja a megfelelő helyet az alkalmazás regisztrációs folyamat részeként.

 A válasz URL-címekre a következő korlátozások vonatkoznak:

    * A válasz URL-címének `https`a sémával kell kezdődnie.
    * A válasz URL-címe a kis- és nagybetűket is figyelembe vevő. Az esetnek meg kell egyeznie a futó alkalmazás URL-elérési útjának esetével. Ha például az alkalmazás az elérési `.../abc/response-oidc`út részeként szerepel, ne adja meg `.../ABC/response-oidc` a válasz URL-címét. Mivel a webböngésző az elérési utakat a `.../abc/response-oidc` kis- és nagybetűk megkülönböztetéseként kezeli, a `.../ABC/response-oidc` kapcsolódó cookie-k kizárhatók, ha az okat nem egyező URL-re irányítják át.
    
## <a name="maximum-number-of-redirect-uris"></a>Átirányítási URI-k maximális száma

Az alábbi táblázat az alkalmazás regisztrálásakor hozzáadható átirányítási URI-k maximális számát mutatja be.

| Bejelentkezett fiókok | Átirányítási URI-k maximális száma | Leírás |
|--------------------------|---------------------------------|-------------|
| Microsoft munkahelyi vagy iskolai fiókok bármely szervezet Azure Active Directory (Azure AD) bérlőjében | 256 | `signInAudience`az alkalmazásjegyzék mezője *AzureADMyOrg* vagy *AzureADMultipleOrgs* lesz állítva |
| Személyes Microsoft-fiókok, valamint munkahelyi és iskolai fiókok | 100 | `signInAudience`az alkalmazásjegyzék mezője *AzureAD ésPersonalMicrosoftAccount* lesz állítva |

## <a name="maximum-uri-length"></a>Maximális URI-hossz

Az alkalmazásregisztrációhoz hozzáadott minden egyes átirányítási URI-hoz legfeljebb 256 karaktert használhat.

## <a name="supported-schemes"></a>Támogatott rendszerek
Az Azure AD-alkalmazásmodell ma már támogatja a HTTP-és HTTPS-sémákat is, amelyek bármely szervezet Azure Active Directory (Azure AD) bérlőjében jelentkeznek be a Microsoft munkahelyi vagy iskolai fiókokba. Ez `signInAudience` a mező az alkalmazásjegyzékben van beállítva, hogy vagy *AzureADMyOrg* vagy *AzureADMultipleOrgs.* A személyes Microsoft-fiókokba bejelentkező alkalmazások, valamint `signInAudience` a munkahelyi és iskolai fiókok (amely *Az AzureAD ésPersonalMicrosoftAccount*értékre van állítva) csak https-séma engedélyezett.

> [!NOTE]
> Az új [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) nem teszik lehetővé a fejlesztők számára, hogy HTTP-sémával rendelkező URI-kat adjanak hozzá a felhasználói felületen. A munkahelyi vagy iskolai fiókokba bejelentkező alkalmazások HTTP-URI-k hozzáadása csak az alkalmazásjegyzék-szerkesztőn keresztül támogatott. A továbbiakban az új alkalmazások nem fogják tudni használni a HTTP-sémákat az átirányítási URI-ban. Az átirányítási URI-kban HTTP-sémákat tartalmazó régebbi alkalmazások azonban továbbra is működni fognak. A fejlesztőknek HTTPS-sémákat kell használniuk az átirányítási URI-kban.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Az URI-kban helyettesítő karaktert használó korlátozások

A helyettesítő urik, `https://*.contoso.com`például a , kényelmesek, de el kell kerülni őket. Az átirányítási URI-ban helyettesítő karakterek használata biztonsági következményekkel jár. Az OAuth 2.0 specifikáció[(a 6749-es számú RFC 3.1.2 szakasza)](https://tools.ietf.org/html/rfc6749#section-3.1.2)szerint az átirányítási végpont URI-jának abszolút URI-nak kell lennie. 

Az Azure AD alkalmazásmodell nem támogatja a helyettesítő uri-k at a személyes Microsoft-fiókokés munkahelyi vagy iskolai fiókok bejelentkezésére konfigurált alkalmazások hoz létre. A helyettesítő karakteres URI-k azonban engedélyezettek olyan alkalmazások esetében, amelyek a szervezet Azure AD-bérlőjében lévő munkahelyi vagy iskolai fiókok bejelentkezésére vannak konfigurálva. 
 
> [!NOTE]
> Az új [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) nem teszik lehetővé a fejlesztők számára, hogy helyettesítő URI-kat adjanak hozzá a felhasználói felülethez. Wilcard URI hozzáadása a munkahelyi vagy iskolai fiókokat bejelentkező alkalmazásokhoz csak az alkalmazásjegyzék-szerkesztő n keresztül támogatott. A továbbiakban az új alkalmazások nem fogják tudni használni a helyettesítő karaktereket az átirányítási URI-ban. Az átirányítási URI-kban helyettesítő karaktereket tartalmazó régebbi alkalmazások azonban továbbra is működni fognak.

Ha a forgatókönyv több átirányítási URI-t igényel, mint a maximálisan megengedett korlát, helyettesítő átirányítási URI hozzáadása helyett vegye figyelembe a következő megközelítést.

### <a name="use-a-state-parameter"></a>Állapotparaméter használata

Ha több altartománnyal rendelkezik, és ha a forgatókönyv megköveteli, hogy a felhasználókat a sikeres hitelesítést követően ugyanarra az oldalra irányítsa át, ahol elkezdték, akkor egy állapotparaméter használata hasznos lehet. 

Ebben a megközelítésben:

1. Hozzon létre egy "megosztott" átirányítási URI alkalmazásonként az engedélyezési végponttól kapott biztonsági jogkivonatok feldolgozásához.
1. Az alkalmazás küldhet alkalmazásspecifikus paramétereket (például altartomány URL-címét, ahonnan a felhasználó származik, vagy bármi hasonló márkajelzési információkat) az állapot paraméterben. Állapotparaméter használata esetén védekezz a [6749.](https://tools.ietf.org/html/rfc6749#section-10.12) 
1. Az alkalmazásspecifikus paraméterek tartalmazzák az összes olyan információt, amely az alkalmazáshoz szükséges a megfelelő felhasználói élmény megjelenítéséhez, azaz a megfelelő alkalmazásállapot létrehozásához. Az Azure AD engedélyezési végpont csíkok HTML az állapot paraméter, ezért győződjön meg arról, hogy nem ad át HTML-tartalom ebben a paraméterben.
1. Amikor az Azure AD választ küld a "megosztott" átirányítási URI-ra, visszaküldi az állapotparamétert az alkalmazásnak.
1. Az alkalmazás ezután az állapotparaméterben szereplő érték alapján meghatározhatja, hogy melyik URL-címet küldje tovább a felhasználónak. Győződjön meg róla, hogy érvényesíti a CSRF védelmet.

> [!NOTE]
> Ez a megközelítés lehetővé teszi, hogy a feltört ügyfél módosítsa az állapotparaméterben küldött további paramétereket, így átirányítva a felhasználót egy másik URL-címre, amely a 6819-es számú RFC-ben leírt [nyílt átirányító fenyegetés.](https://tools.ietf.org/html/rfc6819#section-4.2.4) Ezért az ügyfélnek meg kell védenie ezeket a paramétereket az állapot titkosításával, vagy más módon, például a tartománynév érvényesítésével az átirányítási URI-ban a jogkivonattal szemben.

## <a name="next-steps"></a>További lépések

- További információ az [alkalmazásjegyzékről](reference-app-manifest.md)
