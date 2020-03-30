---
title: Egyoldalas alkalmazások az Azure Active Directoryban
description: Leírja, hogy milyen egyoldalas alkalmazások (SK-k) és az alapismeretek a protokoll-folyamat, a regisztráció és a jogkivonat lejárati ezen alkalmazástípushoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154746"
---
# <a name="single-page-applications"></a>Egyoldalas alkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Az egyoldalas alkalmazások (SpA-k) általában javascript-bemutatórétegként (előtérként) épülnek fel, amely a böngészőben fut, és egy web API háttérrendszerként, amely egy kiszolgálón fut, és megvalósítja az alkalmazás üzleti logikáját. Ha többet szeretne megtudni az implicit engedélyezési támogatásról, és segíthet eldönteni, hogy megfelelő-e az alkalmazásforgatókönyvnek, [olvassa el az OAuth2 implicit támogatási folyamat ának ismertetése az Azure Active Directoryban című témakört.](v1-oauth2-implicit-grant-flow.md)

Ebben a forgatókönyvben, amikor a felhasználó bejelentkezik, a JavaScript előtér az [Active Directory hitelesítési könyvtár JavaScript (ADAL). JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) és az implicit engedélyezési támogatás egy azonosító jogkivonat (id_token) beszerzéséhez az Azure AD-től. A jogkivonat gyorsítótárazott, és az ügyfél csatolja a kérelemhez, mint a tulajdonosi jogkivonat, amikor a webes API-háttérrendszer, amely az OWIN köztes szoftver használatával védett.

## <a name="diagram"></a>Ábra

![Egyoldalas alkalmazásdiagram](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protokoll-folyamat

1. A felhasználó a webalkalmazásra navigál.
1. Az alkalmazás visszaadja a JavaScript előtér (bemutató réteg) a böngészőbe.
1. A felhasználó beindítja a bejelentkezést, például egy bejelentkezési hivatkozásra kattintva. A böngésző get-t küld az Azure AD engedélyezési végpontegy azonosító jogkivonat kéréséhez. Ez a kérelem tartalmazza az alkalmazás azonosítóját és a válasz URL-címét a lekérdezési paraméterekben.
1. Az Azure AD érvényesíti a válasz URL-t az Azure Portalon konfigurált regisztrált válasz URL-címével.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Ha a hitelesítés sikeres, az Azure AD létrehoz egy azonosító jogkivonatot, és azt egy URL-töredékként (#) adja vissza az alkalmazás válasz URL-címére. Éles alkalmazás esetén ez a válasz URL-cím kell HTTPS. A visszaadott jogkivonat tartalmazza a felhasználó és az Azure AD, amelyek az alkalmazás által a jogkivonat érvényesítéséhez szükséges jogcímek.
1. A böngészőben futó JavaScript-ügyfélkód kinyeri a jogkivonatot az alkalmazás webes API-háttérrendszerébe irányuló hívások biztosításához használt válaszból.
1. A böngésző meghívja az alkalmazás webes API-háttértartalékaz engedélyezési fejlécben az azonosító jogkivonattal. Az Azure AD hitelesítési szolgáltatás kiad egy azonosító jogkivonatot, amely tulajdonosi jogkivonatként használható, ha az erőforrás megegyezik az ügyfélazonosítóval (ebben az esetben ez igaz, mivel a webes API az alkalmazás saját háttérrendszer).

## <a name="code-samples"></a>Kódminták

Tekintse meg az [egyoldalas alkalmazási forgatókönyvek kódmintáit.](sample-v1-code.md#single-page-applications) Ügyeljen arra, hogy gyakran ellenőrizze vissza, mivel az új mintákat gyakran adják hozzá.

## <a name="app-registration"></a>Alkalmazásregisztráció

* Egyetlen bérlő – Ha csak a szervezet számára hoz létre alkalmazást, az Azure Portal használatával regisztrálnikell a vállalati címtárban.
* Több-bérlős – Ha olyan alkalmazást hoz össze, amelyet a szervezeten kívüli felhasználók is használhatnak, akkor azt regisztrálni kell a vállalat címtárában, de regisztrálni kell minden egyes szervezet címtárában, amely az alkalmazást fogja használni. Ahhoz, hogy az alkalmazás elérhető vé válik a címtárban, megadhat egy regisztrációs folyamatot az ügyfelek számára, amely lehetővé teszi számukra, hogy hozzájáruljanak az alkalmazáshoz. Amikor regisztrálnak az alkalmazásra, megjelenik egy párbeszédablak, amely megjeleníti az alkalmazás által igényelt engedélyeket, majd a hozzájárulás lehetőségét. A szükséges engedélyektől függően előfordulhat, hogy a másik szervezet rendszergazdájának kell beleegyezést adnia. Amikor a felhasználó vagy a rendszergazda hozzájárul, az alkalmazás regisztrálva lesz a címtárban.

Az alkalmazás regisztrálása után úgy kell konfigurálni, hogy oauth 2.0 implicit támogatási protokollt használjon. Alapértelmezés szerint ez a protokoll le van tiltva az alkalmazások nál. Az OAuth2 implicit támogatási protokoll engedélyezéséhez az alkalmazásjegyzéket az Azure Portalról, és állítsa az "oauth2AllowImplicitFlow" értéket true értékre. További információ: [Application manifest](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Token lejárata

Az ADAL.js használata segít:

* Lejárt jogkivonat frissítése
* Webes API-erőforrás hívásához hozzáférési jogkivonat kérése

Sikeres hitelesítés után az Azure AD egy cookie-t ír a felhasználó böngészőjében egy munkamenet létrehozásához. Vegye figyelembe, hogy a munkamenet a felhasználó és az Azure AD között létezik (nem a felhasználó és a webalkalmazás között). Amikor egy jogkivonat lejár, az ADAL.js ezt a munkamenetet egy másik jogkivonat csendes beszerzésére használja. Az ADAL.js rejtett iFrame-et használ a kérelem küldéséhez és fogadásához az OAuth implicit támogatási protokoll használatával. Az ADAL.js is használhatja ugyanezt a mechanizmust, hogy csendben szerezzen hozzáférési jogkivonatokat más webes API-erőforrásokhoz, amelyeket az alkalmazás hív, mindaddig, amíg ezek az erőforrások támogatják a forrásközi erőforrások megosztását (CORS), regisztrálva vannak a felhasználó címtárában, és minden szükséges hozzájárulás a felhasználó által a bejelentkezés során megadott adatokat.

## <a name="next-steps"></a>További lépések

* További információ az [egyéb alkalmazástípusokról és -forgatókönyvekről](app-types.md)
* Ismerje meg az Azure [AD-hitelesítés alapjait](v1-authentication-scenarios.md)
