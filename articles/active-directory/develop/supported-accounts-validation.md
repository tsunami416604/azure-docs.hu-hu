---
title: Érvényesítési különbségek támogatott fióktípusok szerint – Microsoft identity platform | Azure
description: Ismerje meg a különböző támogatott fióktípusok különböző tulajdonságainak érvényesítési különbségeit, amikor regisztrálja az alkalmazást a Microsoft identitásplatformra.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 10/12/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 2a1507b008903085886f9392f3f4e5461997b6e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128860"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Érvényesítési különbségek támogatott fióktípusok szerint (signInAudience)

Amikor regisztrál egy alkalmazást a Microsoft identity platform a fejlesztők számára, meg kell adnia, hogy milyen fióktípusokat támogat az alkalmazás. Az alkalmazásobjektumban és a `signInAudience`jegyzékfájlban ez a tulajdonság a .

A lehetőségek a következők:

- *AzureADMyOrg:* Csak fiókok a szervezeti címtárban, ahol az alkalmazás regisztrálva van (egybérlős)
- *AzureADMultipleOrgs*: Fiókok bármely szervezeti címtárban (több-bérlős)
- *AzureAD ésPersonalMicrosoftAccount*: Bármely szervezeti címtárban (több-bérlős) és személyes Microsoft-fiókban (például Skype-on, Xbox- és Outlook.com) lévő fiókok

Regisztrált alkalmazások esetén a Támogatott fióktípusok értékét az alkalmazás **Hitelesítés** szakaszában találja. A szálláshely alatt is `signInAudience` megtalálható a **jegyzékben.**

A tulajdonsághoz kiválasztott érték hatással van az alkalmazásobjektum más tulajdonságaira is. Ennek eredményeképpen, ha módosítja ezt a tulajdonságot, előfordulhat, hogy először más tulajdonságokat kell módosítania.

Az alábbi táblázatban a különböző támogatott fióktípusok különböző tulajdonságainak ellenőrzési különbségeit.

| Tulajdonság | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` és `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Alkalmazásazonosító URI`identifierURIs`( )  | Egyedinek kell lennie a bérlőben <br><br> urn:// rendszerek támogatottak <br><br> A helyettesítő karakterek nem támogatottak <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszúsága <br><br> Nincs korlátozás* az azonosítóuri-k számára  | Globálisan egyedinek kell lennie <br><br> urn:// rendszerek támogatottak <br><br> A helyettesítő karakterek nem támogatottak <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszúsága <br><br> Nincs korlátozás* az azonosítóuri-k számára | Globálisan egyedinek kell lennie <br><br> urn:// rendszerek nem támogatottak <br><br> A helyettesítő karakterek, töredékek és lekérdezési karakterláncok nem támogatottak <br><br> Legfeljebb 120 karakter hosszúsága <br><br> Legfeljebb 50 azonosítóURI |
| Tanúsítványok`keyCredentials`( ) | Szimmetrikus aláíró kulcs | Szimmetrikus aláíró kulcs | Titkosítási és aszimmetrikus aláíró kulcs | 
| Ügyféltitkok`passwordCredentials`( ) | Nincs korlátozás* | Nincs korlátozás* | Ha a liveSDK engedélyezve van: Legfeljebb 2 ügyféltitok | 
| Irányított URI-k`replyURLs`( ) | További információ: [Az URI/reply URL-korlátozások átirányítása és](reply-url.md) korlátozásai. | | | 
| API-engedélyek`requiredResourceAccess`( ) | Nincs korlátozás* | Nincs korlátozás* | Engedélyezett erőforrásonként legfeljebb 30 engedély (pl. Microsoft Graph) | 
| Az API által meghatározott`oauth2Permissions`hatókörök ( ) | A hatókör maximális hossza 120 karakter <br><br> Nincs korlátozva* a megadott hatókörök számára | A hatókör maximális hossza 120 karakter <br><br> Nincs korlátozva* a megadott hatókörök számára |  A hatókör maximális hossza 40 karakter <br><br> Legfeljebb 100 definiált hatókör | 
| Engedélyezett ügyfélalkalmazások`preautorizedApplications`( ) | Nincs korlátozás* | Nincs korlátozás* | Összesen maximum 500 <br><br> Legfeljebb 100 ügyfélalkalmazás van definiálva <br><br> Ügyfelenként legfeljebb 30 hatókör | 
| appRoles alkalmazás | Támogatott <br> Nincs korlátozás* | Támogatott <br> Nincs korlátozás* | Nem támogatott | 
| Kijelentkezés URL-címe | http://localhostengedélyezett <br><br> Legfeljebb 255 karakter hosszúsága | http://localhostengedélyezett <br><br> Legfeljebb 255 karakter hosszúsága | <br><br> https://localhostmegengedett, http://localhost sikertelen az MSA-ban <br><br> Legfeljebb 255 karakter hosszúsága <br><br> Http-séma nem engedélyezett <br><br> A helyettesítő karakterek nem támogatottak | 

*Az alkalmazásobjektum összes gyűjteménytulajdonságában körülbelül 1000 elem van korlátozva

## <a name="next-steps"></a>További lépések

- További információ [az alkalmazásregisztrációról](app-objects-and-service-principals.md)
- További információ az [alkalmazásjegyzékről](reference-app-manifest.md)
