---
title: Átirányítási URI-t/válasz URL-cím korlátozások és korlátozásai – a Microsoft identity platform
description: Válasz URL-címek/átirányítási URL-címek korlátozások és korlátozások
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486232"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Átirányítási URI/válasz URL-cím korlátozásai

Átirányítási URI-t vagy a válasz URL-cím, a helyet, hogy az engedélyezési kiszolgáló fog küldeni a felhasználó többször az alkalmazás sikeresen engedélyezve lett, és a egy engedélyezési kód vagy hozzáférést kapott jogkivonatok. A kód vagy a tokenen szerepel az átirányítási URI-t vagy a válasz token, ezért fontos, hogy regisztrálja az alkalmazás regisztrációs folyamat részeként a megfelelő helyre.

## <a name="maximum-number-of-redirect-uris"></a>Átirányítási URI-k maximális száma

Az alábbi táblázat az átirányítási URI-nak, hogy az alkalmazás regisztrálásakor is hozzáadhat a maximális számát. 

| Éppen bejelentkezett fiókok | Átirányítási URI-k maximális száma | Leírás |
|--------------------------|---------------------------------|-------------|
| A Microsoft munkahelyi vagy iskolai fiókok bármely szervezet Azure Active Directory (Azure AD) bérlő | 256 | `signInAudience` az alkalmazásjegyzékben mező értéke termékeken *AzureADMyOrg* vagy *AzureADMultipleOrgs* |
| Személyes Microsoft-fiókok és a munkahelyi és iskolai fiókok | 100 | `signInAudience` az alkalmazásjegyzékben mező értéke *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Hosszabb URI

Minden átirányítási URI-t, amely egy alkalmazásregisztráció ad hozzá egy legfeljebb 256 karakter használható.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Helyettesítő karakterek használatával az URI-k korlátozásai

Helyettesítő karaktert tartalmazó URI-k, például `https://*.contoso.com`, kényelmesek, de el kell kerülni. Helyettesítő karakterek használata az átirányítási URI biztonsági hatással van. Az OAuth 2.0 ismertetőjének megfelelően ([szakasz az RFC 6749 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2)), egy átirányítási végpont URI-t egy abszolút URI Azonosítónak kell lennie. 

Az Azure AD-alkalmazásmodell nem támogatja a helyettesítő karaktert tartalmazó URI-k alkalmazásokat, jelentkezzen be személyes Microsoft-fiókok és a munkahelyi vagy iskolai fiókokhoz. Azonban a helyettesítő karaktert tartalmazó URI-k engedélyezettek, jelentkezzen be munkahelyi vagy ma iskolai fiókokat a szervezet Azure AD-bérlőjében konfigurált alkalmazások. 
 
> [!NOTE]
> Az új [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élmény nem teszi lehetővé a fejlesztők számára a helyettesítő karaktert tartalmazó URI-k hozzáadása a felhasználói felületen. Csak az alkalmazás alkalmazásjegyzék-szerkesztőben keresztül támogatott helyettesítő URI-t az alkalmazásokhoz, melyek jelentkezzen be munkahelyi vagy iskolai fiókok hozzáadása. Továbbítja, új alkalmazásokat nem lehet helyettesítő karakterek használata az átirányítási URI-t. Azonban régebbi átirányítási helyettesítő karaktereket tartalmazó alkalmazások URI-k továbbra is működni fog.

Ha további átirányítási URI-k, mint a maximálisan engedélyezett, hanem helyettesítő átirányítási URI-t, vegye figyelembe a következő megközelítések egyikét.

### <a name="use-a-state-parameter"></a>A state paraméter használata

Ha rendelkezik egy altartományok számát, és ha sikeres hitelesítést követően a felhasználók ugyanazt a lapot, ahol elkezdett irányíthatja, hasznos lehet a state paraméter használatával. 

Ebben a megközelítésben:

1. Hozzon létre egy "megosztott" átirányítási URI-JÁNAK feldolgozni a biztonsági jogkivonatokat kap az engedélyezési végpont alkalmazásonként.
1. Az alkalmazás alkalmazás-specifikus paramétereket (például ha a felhasználó adja meg, vagy bármi, például védjegyzési információinak altartományok URL) elküldheti a state paraméterben. A state paraméter használatakor az megvédje CSRF védelmi megadott [szakasz az RFC 6749 10.12](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Az alkalmazás-specifikus paramétereket jelennek meg a helyes-e az alkalmazás élmény a felhasználó számára, azaz, hozza létre a megfelelő alkalmazásállapot szükséges összes információt tartalmazza. Az Azure AD engedélyezési végpont sávok HTML a state paraméterben, ezért ügyeljen arra, nem megfelelőek HTML tartalom ebben a paraméterben.
1. Az Azure AD átirányítási URI-JÁNAK "megosztott" választ küld, amikor a state paraméterben küld az alkalmazásnak.
1. Az alkalmazás felhasználhatja az értéket a state paraméterben további küldése a felhasználót, hogy mely URL-cím meghatározásához. Ellenőrizze, hogy ellenőrizze, hogy CSRF védelemre.

> [!NOTE]
> Ez a megközelítés lehetővé teszi, hogy egy sérült biztonságú ügyfél a további paraméterek, a state paraméterben, amelyik átirányítja az a felhasználó egy másik URL-címet, amely a küldött módosításához a [nyissa meg az átirányító threat](https://tools.ietf.org/html/rfc6819#section-4.2.4) RFC 6819 ismertetett. Ezért az ügyfél kell védi ezeket a paramétereket az állapot titkosítása vagy ellenőrzése, például az átirányítási URI-t a jogkivonat alapján tartománynév érvényesítése más módon.

### <a name="add-redirect-uris-to-service-principals"></a>Adjon hozzá átirányítási URI-k, az egyszerű szolgáltatások

Másik megoldás, adjon hozzá átirányítási URI-k, a [egyszerű szolgáltatások](app-objects-and-service-principals.md#application-and-service-principal-relationship) , amelyek az alkalmazás regisztrációját minden olyan Azure AD-bérlőben. Ez a megközelítés is használhatja, a state paraméter nem használható, vagy a forgatókönyv szükséges hozzá az új átirányítási URI-k az alkalmazás minden támogatott új bérlőhöz. 

## <a name="next-steps"></a>További lépések

- További információ a [Application manifest](reference-app-manifest.md)
