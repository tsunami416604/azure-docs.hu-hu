---
title: Átirányítási URI/válasz URL-korlátozások és korlátozások – Microsoft Identity platform
description: Válasz URL-címek/átirányítási URL-címek korlátozása & korlátozásai
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1702a0c7ab2d2a76e6ec0e8b217539804a683ff7
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834816"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Átirányítási URI/válasz URL-cím korlátozásai

Az átirányítási URI-vagy válasz-URL-cím az a hely, amelyet az engedélyezési kiszolgáló az alkalmazás sikeres engedélyezése után elküld a felhasználónak, és engedélyezési kódot vagy hozzáférési jogkivonatot adott meg. A kód vagy token az átirányítási URI vagy a válasz jogkivonatában található, ezért fontos, hogy regisztrálja a megfelelő helyet az alkalmazás regisztrációs folyamatának részeként.

## <a name="maximum-number-of-redirect-uris"></a>Átirányítási URI-k maximális száma

A következő táblázat az alkalmazás regisztrálásakor felvehető átirányítási URI-k maximális számát mutatja. 

| Bejelentkezett fiókok | Átirányítási URI-k maximális száma | Leírás |
|--------------------------|---------------------------------|-------------|
| Microsoft munkahelyi vagy iskolai fiókok bármely szervezet Azure Active Directory (Azure AD) bérlőben | 256 | `signInAudience`az alkalmazás jegyzékfájljában lévő mező beállítása *AzureADMyOrg* vagy *AzureADMultipleOrgs* |
| Személyes Microsoft-fiókok és munkahelyi és iskolai fiókok | 100 | `signInAudience`az alkalmazás jegyzékfájljának mezője *AzureADandPersonalMicrosoftAccount* értékre van állítva |

## <a name="maximum-uri-length"></a>URI maximális hossza

Az alkalmazások regisztrálásához hozzáadott átirányítási URI-azonosítóhoz legfeljebb 256 karaktert használhat.

## <a name="restrictions-using-a-wildcard-in-uris"></a>A helyettesítő karakterek használata URI-k használatával

A helyettesítő karakteres URI `https://*.contoso.com`-k (például) kényelmesek, de elkerülhetők. Az átirányítási URI-ban a helyettesítő karakterek használata biztonsági következményekkel jár. Az OAuth 2,0 specifikációnak megfelelően (az[RFC 6749 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2). szakasza) az átirányítási VÉGPONT URI azonosítójának abszolút URI-nak kell lennie. 

Az Azure AD-alkalmazás modelljében nem támogatottak a személyes Microsoft-fiókokhoz és munkahelyi vagy iskolai fiókokhoz való bejelentkezésre konfigurált alkalmazások helyettesítő URI-azonosítói. A helyettesítő URI-k használata azonban engedélyezett olyan alkalmazások esetében, amelyek a munkahelyi vagy iskolai fiókoknak a szervezet Azure AD-bérlőben való bejelentkezésére vannak konfigurálva. 
 
> [!NOTE]
> Az új [Alkalmazásregisztrációki](https://go.microsoft.com/fwlink/?linkid=2083908) felület nem teszi lehetővé a fejlesztők számára, hogy helyettesítő URI-ket adjanak hozzá a felhasználói felületen. A munkahelyi vagy iskolai fiókokat bejelentkező alkalmazások wilcard URI-ja csak az alkalmazás jegyzékfájl-szerkesztőjében támogatott. Az új alkalmazások nem fogják tudni használni a helyettesítő karaktereket az átirányítási URI-ban. Az átirányítási URI-k helyettesítő karaktereit tartalmazó régebbi alkalmazások azonban továbbra is működni fognak.

Ha a forgatókönyv több átirányítási URI-t igényel, mint az engedélyezett maximális korlát, a helyettesítő karakteres átirányítási URI-k hozzáadása helyett vegye figyelembe az alábbi módszerek egyikét.

### <a name="use-a-state-parameter"></a>Állapot paraméterének használata

Ha több altartománya van, és ha a forgatókönyve megköveteli, hogy a felhasználókat a sikeres hitelesítésre irányítsa át ugyanarra az oldalra, ahol elkezdődtek, az állapot paraméterének használata hasznos lehet. 

Ebben a megközelítésben:

1. Hozzon létre egy "Shared" átirányítási URI-t az alkalmazásban az engedélyezési végponttól kapott biztonsági jogkivonatok feldolgozásához.
1. Az alkalmazás elküldheti az alkalmazásspecifikus paramétereket (például altartomány URL-címét, ahol a felhasználó származik, vagy bármi más, mint a márkaépítési információ) az állapot paraméterben. A State paraméter használatakor az CSRF elleni védelem az [RFC 6749 10,12](https://tools.ietf.org/html/rfc6749#section-10.12). szakaszának megfelelően van megadva. 
1. Az alkalmazásspecifikus paraméterek tartalmazzák az alkalmazás számára a megfelelő felhasználói élmény megjelenítéséhez szükséges összes információt, azaz a megfelelő alkalmazás-állapotot. Az Azure AD engedélyezési végpontja a HTML-kódot az állapot paraméterből adja meg, ezért ügyeljen arra, hogy ne legyenek átadva a HTML-tartalom ebben a paraméterben.
1. Ha az Azure AD választ küld a "Shared" átirányítási URI-nak, az állapot-paraméter visszakerül az alkalmazásnak.
1. Az alkalmazás ezután használhatja az állapot paraméter értékét annak meghatározásához, hogy melyik URL-címet szeretné elküldeni a felhasználónak. Győződjön meg arról, hogy a CSRF-védelem érvényesítése megtörtént.

> [!NOTE]
> Ez a módszer lehetővé teszi a feltört ügyfél számára, hogy módosítsa az állapot paraméterében eljuttatott további paramétereket, így átirányítja a felhasználót egy másik URL-címre, amely az RFC 6819-ben leírt [nyílt átirányító fenyegetés](https://tools.ietf.org/html/rfc6819#section-4.2.4) . Ezért az ügyfélnek védenie kell ezeket a paramétereket az állapot titkosításával vagy más módon történő ellenőrzésével, például az átirányítási URI-azonosítóban lévő tartománynév érvényesítésével.

### <a name="add-redirect-uris-to-service-principals"></a>Átirányítási URI-k hozzáadása az egyszerű szolgáltatásokhoz

Egy másik módszer az, hogy átirányítási URI [](app-objects-and-service-principals.md#application-and-service-principal-relationship) -ket adjon hozzá az alkalmazás regisztrálásához bármely Azure ad-bérlőben. Ezt a módszert akkor használhatja, ha nem használhat State paramétert, vagy ha a forgatókönyv megköveteli, hogy új átirányítási URI azonosítókat adjon az alkalmazás regisztrálásához minden Ön által támogatott új bérlőhöz. 

## <a name="next-steps"></a>További lépések

- Az [alkalmazás jegyzékfájljának](reference-app-manifest.md) megismerése
